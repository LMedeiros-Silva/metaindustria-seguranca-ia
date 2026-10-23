# Manual de Execução — SafeVision Metaindústria / 2Identify

Guia para configurar, iniciar e testar o projeto em outro computador **Windows 10/11** usando
**PowerShell**. Os comandos são os mesmos do [README principal](../README.md#6-instalação-e-execução-no-windows)
e foram executados em 22/10/2026 num banco PostgreSQL 18.6 vazio, com Python 3.12.10.

Convenção: o repositório é clonado em `C:\dev\metaindustria-seguranca-ia` (chamado de **raiz**).
Todos os comandos Python usam o interpretador da `.venv` de cada módulo.

---

## Visão rápida

| # | Etapa | Onde | Seção |
|---|---|---|---|
| 1 | Instalar Python 3.12, PostgreSQL 18 e Git | Windows | [1](#1-pré-requisitos) |
| 2 | Criar role `identify_user` e banco `identify_db` | psql | [2](#2-banco-de-dados) |
| 3 | Clonar, criar `.venv` e instalar dependências | raiz | [3](#3-código-e-dependências) |
| 4 | Criar e preencher os três `.env` | raiz | [4](#4-configuração-env) |
| 5 | Aplicar migrações e rodar o seed | API / Admin | [5](#5-migrações-e-dados-iniciais) |
| 6 | Instalar os modelos de IA | Operador | [6](#6-modelos-de-ia) |
| 7 | Iniciar API → Admin → Operador | 3 terminais | [7](#7-inicialização) |
| 8 | Configurar câmeras, operação e Face ID | Admin | [8](#8-configuração-funcional-no-admin) |
| 9 | Testar o fluxo do operador | Operador | [9](#9-roteiro-de-teste-do-fluxo-principal) |
| 10 | Rodar os testes automatizados | cada módulo | [10](#10-testes-automatizados) |

---

## 1. Pré-requisitos

| Software | Versão | Observação |
|---|---|---|
| Windows | 10/11 64 bits | — |
| Python | 3.12.x 64 bits | Instalador em <https://www.python.org/downloads/windows/>; marque "Add python.exe to PATH". A API exige `>=3.12,<3.13`. |
| PostgreSQL | 18 | Instalador em <https://www.postgresql.org/download/windows/>; anote a senha do `postgres`. |
| Git | recente | <https://git-scm.com/download/win> |
| Node.js | 18+ | Opcional, só para `node --test` do PoC web. |
| Arduino IDE | 2.x | Opcional, só para gravar o ESP32. |

Hardware: webcam (Face ID e monitoramento) ou câmera IP com RTSP; ~3 GB livres para os ambientes
virtuais (o do Operador tem ~1,9 GB por causa do PyTorch).

Conferência:

```powershell
py -3.12 --version
git --version
Get-Service postgresql*
```

## 2. Banco de dados

```powershell
& 'C:\Program Files\PostgreSQL\18\bin\psql.exe' -U postgres -h localhost
```

```sql
CREATE ROLE identify_user LOGIN;
\password identify_user
CREATE DATABASE identify_db OWNER identify_user;
\q
```

O nome `identify_user` é obrigatório (a migração `f1b2c3d4e5f6` executa
`GRANT ... TO identify_user`). O role é dono do banco para poder aplicar as migrações nesta
instalação de avaliação.

## 3. Código e dependências

```powershell
New-Item -ItemType Directory -Force C:\dev | Out-Null
Set-Location C:\dev
git clone https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia.git
```

> Use um caminho curto. Em caminhos longos, o Windows falha ao carregar DLLs do `psycopg2` e do
> PyTorch (`DLL load failed ... nome do arquivo ou a extensão é muito grande`).

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m pip install "pytest==8.4.2" "ruff==0.16.3"

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
```

Opcional: `.\.venv\Scripts\python.exe -m pip check` em cada módulo deve responder
`No broken requirements found.`

## 4. Configuração (`.env`)

### 4.1 Criar os arquivos

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia
Copy-Item 2identify_api\.env.example 2identify_api\.env
Copy-Item 2identify_admin\.env.example 2identify_admin\.env
Copy-Item 2identify_operador\.env.example 2identify_operador\.env
```

Edite com o Bloco de Notas (`notepad <arquivo>`). Evite `Set-Content -Encoding utf8` do
PowerShell 5.1: ele grava BOM e a API deixa de ler a primeira linha (`DATABASE_URL`).

### 4.2 API (`2identify_api\.env`)

| Variável | Valor |
|---|---|
| `DATABASE_URL` | `postgresql+psycopg2://identify_user:SUA_SENHA@localhost:5432/identify_db` (codifique `@ : / # %` na senha, ex.: `@` → `%40`). |
| `AUTH_TOKEN_SECRET` | Gerado pelo script abaixo. |
| `OPERATOR_CATALOG_TOKEN` | Gerado pelo script abaixo (grava também no Operador). |
| `OPERATOR_STATION_TOKEN` | Valor aleatório, **igual** no `.env` do Operador. Necessário para Face ID. |
| `SAFETY_DEVICE_TOKEN` | Valor aleatório, igual ao do firmware; ou vazio (`SAFETY_DEVICE_TOKEN=`) sem ESP32. |
| `MOBILE_CORS_ORIGINS` | Opcional, só para o PoC web: `http://localhost:5173`. |

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m scripts.ensure_auth_secret
.\.venv\Scripts\python.exe -m scripts.ensure_operator_catalog_token
.\.venv\Scripts\python.exe -c "import secrets; print(secrets.token_urlsafe(48))"
```

O último comando imprime um valor novo a cada execução: use um para `OPERATOR_STATION_TOKEN`
(nos dois arquivos) e outro para `SAFETY_DEVICE_TOKEN`. A API recusa iniciar se qualquer token
ainda começar com `CHANGE_ME` ou tiver menos de 32 bytes.

### 4.3 Admin (`2identify_admin\.env`)

- `API_URL=http://127.0.0.1:8000` (padrão).
- `DATABASE_URL` = mesma URL da API — usada **somente** pelo `seed.py`.

### 4.4 Operador (`2identify_operador\.env`)

- `API_URL=http://localhost:8000` (padrão).
- `OPERATOR_CATALOG_TOKEN` já preenchido pelo script; `OPERATOR_STATION_TOKEN` igual ao da API.
- `CAMERA_SOURCE`, `LOGIN_CAMERA_SOURCE` (padrão `0`) e, por câmera cadastrada, `CAMERA_SOURCE_<id>`.
- `PPE_MODEL_PATH=models/ppe/best.pt` e `POSE_MODEL_PATH=models/pose/yolo11n-pose.pt` (padrões).
- `OPERATIONS_MOCK_ENABLED=false` (com `true`, em desenvolvimento, aparece uma lista local de
  operações marcada como demonstrativa).

## 5. Migrações e dados iniciais

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m alembic upgrade head
.\.venv\Scripts\python.exe -m alembic current
```

Esperado: 10 revisões aplicadas, terminando em `a3c5e7f9b1d2 (head)`.

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
.\.venv\Scripts\python.exe seed.py
```

O seed pede a senha do `admin` (mín. 12 caracteres, não exibida) e cria: usuário `admin`, setores
Produção, Almoxarifado, Manutenção e Expedição; EPIs `EPI-001` a `EPI-006`; funcionários
fictícios FUNC001–FUNC003 com EPIs associados. É idempotente (não duplica se rodar de novo).

**Conta de operador (opcional, para login por usuário/senha):** na pasta `2identify_admin`,
execute o bloco da [seção 6.11 do README](../README.md#611-preparar-dados-e-usuários-de-teste).
Ele pede usuário, nome e senha (oculta) e grava a conta com perfil `operador`.

## 6. Modelos de IA

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe scripts\download_face_models.py
.\.venv\Scripts\python.exe scripts\download_pose_model.py
```

- `download_face_models.py` confere os ONNX YuNet/SFace já versionados (responde `OK:`) e os
  baixa do OpenCV Zoo somente se faltarem ou estiverem corrompidos.
- `download_pose_model.py` baixa `yolo11n-pose.pt` do release oficial da Ultralytics e confere o SHA-256.
- O detector de EPI `best.pt` (versão retreinada, 6.254.698 bytes) **já vem no clone**, em
  `2identify_operador\models\ppe\best.pt`. Para conferir a integridade:

```powershell
(Get-FileHash .\models\ppe\best.pt -Algorithm SHA256).Hash
```

O resultado deve ser `961AB27019BADAE6E2F952BD633E655EA1D58F708801A9A4B531CD39EB578B42`
(valor de `PPE_MODEL_SHA256` no `.env` e no padrão do código). Arquivo com outro hash é recusado.
O modelo tem 11 classes: `bota`, `capacete`, `colete_refletivo`, `luva`, `mangote`, `mao_sem_luva`, `mascara`, `oculos`, `protetor_headset`, `protetor_intra`, `tronco_sem_colete`; as oito usadas pelo mapeamento de EPIs
estão todas presentes (validado em 23/10/2026).

Verificação sem interface:

```powershell
.\.venv\Scripts\python.exe main.py --check
```

Retorna 0 quando a configuração e os modelos obrigatórios (faciais e pose) estão presentes. A
linha `operator_startup_check_ok` informa, entre outros, `ppe_model_available` e
`face_station_configured`.

## 7. Inicialização

Abra **três** janelas do PowerShell, nesta ordem.

**Terminal 1 — API**

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --workers 1 --ws-max-size 65536
```

Conferência (outra janela): `Invoke-RestMethod http://127.0.0.1:8000/health` → `status: ok`,
`database: connected`. Swagger em <http://127.0.0.1:8000/docs>.

**Terminal 2 — Admin**

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
.\.venv\Scripts\python.exe main.py
```

**Terminal 3 — Operador**

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe main.py
```

Para encerrar, feche as janelas dos desktops e pressione `Ctrl+C` no terminal da API.

## 8. Configuração funcional no Admin

1. **Login:** usuário `admin` e a senha definida no seed.
2. **Câmera** (Operações e Áreas): nome, setor e fonte. USB = índice (`0`, `1`...); IP = URL
   completa `rtsp://usuario:senha@host:554/caminho`. A API devolve a senha mascarada (`***`) ao Admin.
3. **Área de risco:** "Configurar área de risco" captura uma imagem da câmera; clique para criar
   pelo menos três vértices, feche o polígono e salve. Coordenadas são normalizadas (0–1).
4. **Operação:** nome, descrição, EPIs obrigatórios e área de risco.
5. **Face ID** (Funcionários): selecione o funcionário e clique em **Capturar Face ID**. As capturas
   (frontal, esquerda, direita) usam a câmera do computador do Admin e os modelos ONNX da pasta
   `2identify_operador\models` (por isso os módulos devem ficar lado a lado, como no repositório).

### Câmeras em mais de uma estação

- Índices USB valem apenas para o computador local. Descubra os disponíveis com:

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe scripts\probe_usb_cameras.py
```

- Para substituir a fonte de uma câmera só nesta estação, acrescente no `.env` do Operador
  `CAMERA_SOURCE_<id>=<índice ou URL>` usando o ID real exibido no Admin.
- Guia de campo com o procedimento multicâmera: [docs/2identify/metaindustria-multicamera-field-guide.md](2identify/metaindustria-multicamera-field-guide.md).

## 9. Roteiro de teste do fluxo principal

| Passo | Ação | Resultado esperado |
|---|---|---|
| 1 | Operador: login por Face ID (olhe para a câmera e mova levemente a cabeça) | Boas-vindas com o nome do funcionário. Alternativa: usuário/senha do operador. |
| 2 | Operações: selecione a operação | Detalhes, EPIs obrigatórios, área de risco e manual. |
| 3 | "Começar trabalho" | Tela de verificação com câmera, caixas de detecção e estado de cada EPI (COLETANDO → CONFIRMADO). |
| 4 | Todos os EPIs confirmados | A operação inicia automaticamente (ou pelo botão "Iniciar operação"). |
| 5 | Remova um EPI / incline o tronco além de 45° / entre na área de risco | Alerta local na tela; alerta registrado na API. |
| 6 | Admin | Banner e contador de alertas, Central de segurança atualizada, Gestão de EPIs mostra o estado em tempo real. |
| 7 | Admin → Alertas | Confirme; depois encerre. Encerrar antes de confirmar é bloqueado. |
| 8 | Admin → Relatórios | Gere o Excel com filtros. |
| 9 | Operador → encerrar operação e "Sair" | Sessão descartada e retorno ao login. |

**Sem câmera:** é possível validar a API (`/health`, `/docs`), todo o Admin exceto captura de
imagem/Face ID, e o login do Operador por usuário/senha com listagem de operações.

## 10. Testes automatizados

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m pytest
.\.venv\Scripts\python.exe -m ruff check .

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
$env:QT_QPA_PLATFORM = "offscreen"
.\.venv\Scripts\python.exe -m pytest
.\.venv\Scripts\python.exe -m ruff check .

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
$env:QT_QPA_PLATFORM = "offscreen"
.\.venv\Scripts\python.exe -m pytest
.\.venv\Scripts\python.exe -m ruff check .

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_web
node --test
```

| Módulo | Resultado em 22/10/2026 |
|---|---|
| API | 219 aprovados |
| Admin | 142 aprovados |
| Operador | 510 aprovados, 0 ignorados (23/10/2026, com o `best.pt`) |
| Web | 10 aprovados |
| Ruff (API, Admin, Operador) | sem apontamentos |

Os testes Python não usam o PostgreSQL real nem câmeras.

## 11. Componentes opcionais

### 11.1 PoC web de alertas no celular

1. No `.env` da API, adicione `MOBILE_CORS_ORIGINS=http://localhost:5173` e reinicie a API.
2. Sirva a página:

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_web
py -3.12 -m http.server 5173 --bind 127.0.0.1
```

3. Abra <http://localhost:5173> e entre com o usuário administrador. A página procura a API na
   porta 8000 do mesmo host.

Para acessar pelo celular é preciso expor a API e a página na rede local (`--host 0.0.0.0`) e
incluir a origem `http://IP_DO_PC:5173` em `MOBILE_CORS_ORIGINS` — faça isso apenas em rede
confiável, pois a API não tem HTTPS nem limitação de tentativas.

### 11.2 Torre de sinalização ESP32

Siga o [README do firmware](../2identify_operador/firmware/esp32_safety_signal/README.md):
Arduino IDE com core ESP32 3.3.11, bibliotecas WebSockets 2.7.2 e ArduinoJson 7.4.2; copie
`device_config.example.h` para `device_config.h` (ignorado pelo Git) e preencha Wi-Fi, IP do PC,
porta 8000 e o mesmo `SAFETY_DEVICE_TOKEN` da API. A API precisa escutar na rede local
(`--host 0.0.0.0`) para o ESP32 alcançá-la.

### 11.3 PoC Supabase

Os scripts `configure_mobile_poc`, `prepare_mobile_poc_supabase`, `sync_mobile_poc_to_supabase`
e `run_mobile_poc_api` (pasta `2identify_api\scripts`) exigem um projeto Supabase próprio e o
arquivo `.env.mobile`. Não foram executados nesta entrega.

## 12. Recomeçar do zero

Para apagar o banco de avaliação e repetir a instalação (destrói os dados):

```sql
DROP DATABASE identify_db;
CREATE DATABASE identify_db OWNER identify_user;
```

Depois repita a seção 5.

## 13. Solução de problemas

Consulte a tabela completa em [README → 6.19](../README.md#619-soluções-para-erros-comuns).
Itens mais frequentes: caminho longo demais (clone em `C:\dev`), `.env` com BOM, tokens com
`CHANGE_ME`, porta 8000 ocupada, modelo de pose ausente no `--check` e `best.pt` alterado (hash diferente).

## 14. O que não pôde ser validado nesta entrega

- Fluxo com câmera física: não executado nesta validação técnica. O `best.pt` foi validado fora da
  câmera (carregamento, classes e inferência em quadros sintéticos). O grupo registrou Face ID,
  verificação de EPIs e alerta de ergonomia com câmera real no [vídeo da Sprint 4](https://youtu.be/2VN0RzsWvMM).
- Torre ESP32 física: não executada nesta validação técnica; o vídeo mostra a estrutura montada.
- PoC web com login válido no navegador (validados: carregamento, CORS e rejeição de login inválido; o fluxo completo é coberto pelos testes Node).
- PoC Supabase (exige conta cloud).
