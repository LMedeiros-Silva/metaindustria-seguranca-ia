# SafeVision Metaindústria — 2Identify | Sprint 4

Entrega final da disciplina de Engenharia de Software (FIAP). Este repositório reúne a
documentação produzida da Sprint 1 à Sprint 3 e, a partir da Sprint 4, uma **cópia do código
versionado do 2Identify** — a aplicação que implementa a solução SafeVision.

> O código foi copiado da branch `redesign/fase2-design-system` do repositório
> [LMedeiros-Silva/2identify](https://github.com/LMedeiros-Silva/2identify) (commit `36252b8`).
> Os dois repositórios são independentes: nada foi alterado na origem. Diferenças em relação à
> origem estão listadas em [Diferenças em relação à origem](#diferenças-em-relação-à-origem).

---

## Sumário

1. [Apresentação](#1-apresentação)
2. [Integrantes](#2-integrantes)
3. [Funcionalidades](#3-funcionalidades)
4. [Tecnologias utilizadas](#4-tecnologias-utilizadas)
5. [Arquitetura](#5-arquitetura)
6. [Instalação e execução no Windows](#6-instalação-e-execução-no-windows)
7. [Evolução das Sprints](#7-evolução-das-sprints)
8. [Documentação](#8-documentação)
9. [Links](#9-links)
10. [Status da entrega — Sprint 4](#10-status-da-entrega--sprint-4)

---

## 1. Apresentação

### Descrição do projeto

O **SafeVision Metaindústria** é uma solução de segurança industrial proativa baseada em
Inteligência Artificial e Visão Computacional. Na implementação (2Identify), câmeras monitoram o
trabalhador durante uma operação, verificam os EPIs obrigatórios, fazem uma triagem ergonômica da
postura, detectam a entrada em áreas de risco cadastradas e geram alertas que chegam em tempo real
a uma aplicação administrativa e, opcionalmente, a uma torre de sinalização física (ESP32).

### Problema abordado

Ambientes industriais apresentam riscos relacionados ao uso incorreto de EPIs, posturas
inadequadas e aproximação de áreas perigosas. A fiscalização costuma depender apenas da supervisão
humana, o que atrasa a identificação de situações de risco e dificulta o registro histórico das
ocorrências.

### Objetivos da solução

- Liberar o início de uma operação somente quando os EPIs obrigatórios forem confirmados pela câmera.
- Monitorar continuamente EPIs, postura e áreas de risco durante a operação.
- Gerar alertas com deduplicação e registrá-los de forma persistente e auditável.
- Dar ao gestor de segurança uma central com indicadores, histórico, tratamento de ocorrências e relatórios.
- Sinalizar o estado de segurança no chão de fábrica por meio de uma torre luminosa.

### Benefícios esperados para a Metaindústria

- Identificação mais rápida de não conformidades, sem depender de inspeção visual contínua.
- Histórico rastreável de alertas (quem confirmou, quando foi resolvido) para auditoria e melhoria contínua.
- Relatórios em Excel para análise por setor, operação, câmera, funcionário e severidade.
- Base técnica modular (API + clientes desktop) que pode evoluir para mais câmeras e estações.

> A solução **apoia** a prevenção; não substitui normas de segurança, análise ergonômica por
> profissional qualificado nem intertravamentos de segurança certificados.

### Objetivos da Sprint 4

- Consolidar a aplicação final (Admin, Operador, API, IA, banco e integração com ESP32) no repositório acadêmico.
- Validar a instalação em um ambiente limpo e documentar um tutorial reproduzível para Windows.
- Executar e registrar os testes automatizados disponíveis.
- Documentar arquitetura final, evolução das Sprints e preparar a Sprint Review final.

---

## 2. Integrantes

| Nome | RM |
|---|---|
| Leonardo Medeiros | 559220 |
| Arthur Bergamaço Alves | 556207 |
| Breno Barbosa Pereira | 555348 |
| Mateus de Souza Santos | 559118 |
| Pietro Rodriguez | 555899 |
| Renan Melo Rosembeng | 558535 |

---

## 3. Funcionalidades

Levantamento feito a partir do código copiado. **Legenda:** ✅ implementado e coberto por testes
automatizados · 🟡 parcial · 🧪 experimental / depende de validação física · ⛔ não implementado.

| Área | Status | O que existe no código |
|---|---|---|
| Sistema administrativo (`2identify_admin`) | ✅ / 🟡 | Desktop PySide6 com login, Central de segurança (cards + 3 gráficos), Alertas, Gestão de EPIs em tempo real, Operações e Áreas, Funcionários e Relatórios. A tela **Configurações** é apenas um placeholder. |
| Sistema do operador (`2identify_operador`) | ✅ | Desktop PySide6: login, lista de operações, verificação de segurança pré-operação, operação ativa com monitoramento contínuo, multicâmera, manual em PDF da operação e logout seguro. |
| Autenticação | ✅ | Admin: `POST /auth/admin/login` (JWT com audience própria). Operador: usuário/senha em `POST /auth/login` (bcrypt + JWT). Tokens só em memória. |
| Reconhecimento facial (Face ID) | ✅ / 🧪 | Cadastro do rosto no Admin (frontal, esquerda, direita). No Operador: detecção YuNet, embeddings SFace e prova de vida temporal *baseline*; a **API** compara com os templates (`POST /auth/face`, autenticado pelo token da estação) e emite a sessão. A prova de vida não é um anti-spoofing certificado. |
| Gerenciamento de funcionários | ✅ | Listar, cadastrar e editar funcionários (setor, cargo, turno, ativo) via `/admin/employees` e registrar o template facial. |
| Gerenciamento de EPIs | 🟡 | Catálogo inicial criado pelo `seed.py` (6 EPIs); **não há tela nem endpoint para cadastrar novos EPIs**. EPIs obrigatórios são vinculados às operações. A tela "Gestão de EPIs" mostra em tempo real o estado de cada EPI nas operações ativas. |
| Cadastro de operações | ✅ | Nome, descrição, EPIs obrigatórios e área de risco (`/admin/operations`). |
| Monitoramento por câmeras | ✅ / 🧪 | Câmeras USB (índice local) e IP (RTSP/HTTP) cadastradas no Admin; senha mascarada nas respostas administrativas da API; vínculo local `CAMERA_SOURCE_<id>`; várias câmeras por operação. Funcionamento com câmeras reais depende do hardware. |
| Inteligência Artificial com YOLO | ✅ / 🧪 | Detector de EPI **YOLOv8 customizado** (11 classes, Ultralytics 8.4.115) e **YOLO11n-pose** para pose humana, ambos executados localmente em CPU. O checkpoint de EPI retreinado (`best.pt`) está versionado neste repositório — ver [6.12](#612-modelos-de-ia). |
| Detecção de EPIs | ✅ / 🧪 | Janela temporal de estabilização, decisão *fail-closed* (EPI ausente, inconclusivo ou sem mapeamento bloqueia), tracking por classe, capacete confirmado somente "na cabeça" (pose), fusão de evidências entre câmeras e início automático quando todos os EPIs são confirmados. Precisão em campo não foi medida nesta entrega. |
| Postura e ergonomia | 🧪 | Triagem 2D: inclinação do tronco, braços acima dos ombros e flexão de joelhos (desligada por padrão). Também há triagem de **possível queda**. Limiares precisam de validação por responsável de ergonomia; não é RULA/REBA. |
| Áreas de risco | ✅ / 🧪 | No Admin, o polígono é desenhado sobre uma imagem capturada da câmera (coordenadas normalizadas, validação de auto-interseção). No Operador, a entrada é detectada quando pontos do corpo (pose) entram no polígono calibrado. |
| Geração de alertas | ✅ / 🟡 | Motor local com debounce, deduplicação, cooldown e escalonamento para crítico; envio idempotente à API (`POST /operator/alerts`) com repetição limitada; publicação em tempo real ao Admin. **Não há outbox durável**: um alerta pendente se perde se o Operador for encerrado durante falha prolongada de rede. |
| Histórico e tratamento de ocorrências | ✅ | Histórico paginado e detalhe de cada alerta; confirmar recebimento e encerrar (encerrar antes de confirmar retorna 409); resolução física separada do encerramento administrativo; exportação **Excel (.xlsx)** com filtros (datas, setor, funcionário, operação, câmera, severidade) e abas Resumo, Alertas, Ocorrências e EPIs. |
| Integração com a API | ✅ | Admin e Operador não acessam o banco: todo fluxo passa pela FastAPI (REST + WebSocket `WS /ws/admin/alerts`). |
| Banco de dados | ✅ | PostgreSQL com 10 revisões Alembic; criação do esquema do zero validada. |
| ESP32 e torre de sinalização | ✅ / 🧪 | Firmware Arduino (`2identify_operador/firmware/esp32_safety_signal`) conectado a `WS /ws/devices/safety`: verde/amarelo/vermelho + buzina via relés, watchdog de 30 s. Testado por automação na API; **teste físico não realizado nesta validação**. |
| Alertas no celular (`2identify_web`) | 🧪 | Prova de conceito: página estática com login administrativo e lista de alertas, consumindo a API. Há também scripts de PoC com Supabase (exigem conta cloud própria). |
| Segmentação com SAM | ⛔ | Citado nas Sprints anteriores como tecnologia considerada; **não foi usado** na implementação. |

---

## 4. Tecnologias utilizadas

| Tecnologia | Versão (do código) | Finalidade |
|---|---|---|
| Python | 3.12 (64 bits) | Linguagem de todos os módulos. A API exige `>=3.12,<3.13`. |
| FastAPI + Uvicorn | 0.116.1 / 0.35.0 | API REST e WebSocket que centraliza regras, autenticação e persistência. |
| SQLAlchemy + psycopg2 | 2.0.43 / 2.9.10 | Acesso ao PostgreSQL. |
| Alembic | 1.16.4 | Migrações versionadas do esquema. |
| PostgreSQL | validado com 18.6 | Banco relacional (usuários, funcionários, EPIs, câmeras, operações, áreas de risco, ocorrências, alertas, templates faciais). |
| PyJWT + bcrypt | 2.13.0 / 4.3.0 | Tokens de sessão assinados e hash de senhas. |
| Pydantic / pydantic-settings | 2.x | Validação de contratos e configuração tipada via `.env`. |
| PySide6 (Qt 6) | 6.9.1 (Admin), ≥6.8 (Operador) | Interfaces desktop, gráficos nativos e cliente WebSocket (`QtWebSockets`). |
| httpx | 0.28 | Cliente HTTP dos desktops. |
| OpenCV (headless) | 4.10 | Captura de câmeras USB/IP e modelos faciais YuNet (detecção) e SFace (reconhecimento). |
| Ultralytics YOLO | 8.4.115 | Detector de EPI (YOLOv8 customizado) e estimativa de pose (YOLO11n-pose). |
| PyTorch / torchvision | 2.13.0 / 0.28.0 (CPU) | Runtime de inferência dos modelos YOLO. |
| openpyxl | 3.1 | Geração dos relatórios Excel no Admin. |
| HTML/CSS/JavaScript + Node.js | Node 18+ para testes | PoC web de alertas no celular (`node --test`). |
| ESP32 (Arduino) | core 3.3.11, WebSockets 2.7.2, ArduinoJson 7.4.2 | Firmware da torre de sinalização. |
| pytest, pytest-qt, ruff, mypy | — | Testes automatizados e qualidade de código. |
| Figma, Trello, GitHub, Mermaid | — | Protótipo, gestão Scrum, versionamento e diagramas. |

---

## 5. Arquitetura

O sistema é dividido em módulos independentes, cada um com suas dependências e ambiente virtual:

- **`2identify_api`** — FastAPI. Única peça que acessa o PostgreSQL. Autentica Admin, Operador e
  estações de Face ID; recebe alertas e estados de segurança do Operador; publica eventos em tempo
  real ao Admin (WebSocket) e o estado agregado à torre ESP32.
- **`2identify_admin`** — desktop do gestor: cadastros, central de segurança, tratamento de alertas,
  relatórios e cadastro facial. Conversa somente com a API (o `DATABASE_URL` do Admin é usado apenas
  pelo `seed.py`).
- **`2identify_operador`** — desktop da estação de trabalho: captura de vídeo, inferência local de
  IA (EPI, pose, ergonomia, queda, área de risco), regras de liberação e envio de alertas.
- **`2identify_web`** — PoC estática de alertas para celular.
- **Firmware ESP32** — torre de sinalização ligada à API por WebSocket.

```mermaid
flowchart LR
    subgraph Estacao["Estação do Operador"]
        CAM["Câmeras USB / IP (RTSP)"]
        OP["2identify_operador<br/>PySide6 + OpenCV"]
        IA["IA local<br/>YOLOv8 EPI · YOLO11 pose<br/>YuNet + SFace"]
        CAM --> OP
        OP <--> IA
    end

    subgraph Servidor["Servidor"]
        API["2identify_api<br/>FastAPI + Uvicorn"]
        DB[("PostgreSQL<br/>identify_db")]
        API <-->|SQLAlchemy / Alembic| DB
    end

    ADM["2identify_admin<br/>PySide6"]
    WEB["2identify_web<br/>PoC celular"]
    ESP["ESP32<br/>torre verde/amarelo/vermelho"]

    OP -->|"REST: login, Face ID, operações,<br/>alertas, estado de segurança"| API
    ADM -->|"REST: cadastros, alertas,<br/>dashboard, relatórios"| API
    API -->|"WS /ws/admin/alerts<br/>alertas e EPIs em tempo real"| ADM
    WEB -->|REST| API
    API -->|"WS /ws/devices/safety<br/>GREEN / YELLOW / RED"| ESP
```

Detalhes, decisões e limitações: [docs/arquitetura-final.md](docs/arquitetura-final.md).

---

## 6. Instalação e execução no Windows

Tutorial validado em **Windows 11 + PowerShell 5.1**, partindo de um banco vazio. Uma versão
estendida (câmeras, ESP32, PoC web, checklist) está em
[docs/manual-execucao.md](docs/manual-execucao.md).

**Ordem de inicialização:** PostgreSQL → API → Admin → Operador.

> Os comandos usam `.\.venv\Scripts\python.exe` diretamente, sem `Activate.ps1`, para não depender
> da política de execução do PowerShell. Execute cada bloco na pasta indicada.

### 6.1 Pré-requisitos

| Item | Versão | Obrigatório |
|---|---|---|
| Windows | 10 ou 11, 64 bits | Sim |
| Python | 3.12.x, 64 bits | Sim |
| PostgreSQL | 18 (validado com 18.6) | Sim |
| Git | qualquer versão recente | Sim |
| Espaço em disco | ~3 GB para os ambientes virtuais (PyTorch é o maior) | Sim |
| Internet | para `pip` e para baixar o modelo de pose | Sim, na instalação |
| Webcam ou câmera IP | — | Para Face ID e monitoramento |
| Node.js | 18 ou superior | Só para os testes do PoC web |
| ESP32 + módulo de relés | — | Opcional |

### 6.2 Instalar o Python

1. Baixe o instalador **Windows installer (64-bit)** do Python 3.12 em <https://www.python.org/downloads/windows/>.
2. Marque **"Add python.exe to PATH"** e mantenha o **Python Launcher (`py`)** selecionado.
3. Confira:

```powershell
py -3.12 --version
```

### 6.3 Instalar o PostgreSQL

1. Baixe o instalador do PostgreSQL 18 em <https://www.postgresql.org/download/windows/>.
2. Durante a instalação, defina a senha do usuário administrador `postgres` e mantenha a porta `5432`.
3. Confira se o serviço está rodando:

```powershell
Get-Service postgresql*
```

### 6.4 Criar o banco de dados

Abra o `psql` como administrador (ele pede a senha do `postgres`):

```powershell
& 'C:\Program Files\PostgreSQL\18\bin\psql.exe' -U postgres -h localhost
```

No prompt `postgres=#`, execute:

```sql
CREATE ROLE identify_user LOGIN;
\password identify_user
CREATE DATABASE identify_db OWNER identify_user;
\q
```

- O nome do role **precisa** ser `identify_user`: uma migração concede permissões a esse role.
- `\password` pede a senha sem exibi-la. Guarde-a para o passo 6.9.
- Tornar `identify_user` dono do banco simplifica a instalação de avaliação (ele aplica as
  migrações). Em produção, separe o role de migração do role de execução.

### 6.5 Clonar o repositório

Use uma pasta de caminho **curto** (o Windows limita caminhos a 260 caracteres e o PyTorch tem
arquivos com nomes longos):

```powershell
New-Item -ItemType Directory -Force C:\dev | Out-Null
Set-Location C:\dev
git clone https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia.git
Set-Location C:\dev\metaindustria-seguranca-ia
```

### 6.6 Criar os ambientes virtuais

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
py -3.12 -m venv .venv

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
py -3.12 -m venv .venv

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
py -3.12 -m venv .venv
```

### 6.7 Instalar as dependências

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m pip install "pytest==8.4.2" "ruff==0.16.3"

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
```

`requirements-dev.txt` do Operador inclui `requirements.txt` (PyTorch CPU, Ultralytics, OpenCV,
PySide6) e as ferramentas de teste. A instalação do Operador é a mais demorada.

### 6.8 Criar os arquivos `.env`

Crie **os três** arquivos antes de qualquer script (um dos scripts escreve no `.env` do Operador):

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia
Copy-Item 2identify_api\.env.example 2identify_api\.env
Copy-Item 2identify_admin\.env.example 2identify_admin\.env
Copy-Item 2identify_operador\.env.example 2identify_operador\.env
```

Os `.env` são ignorados pelo Git. Edite-os com o **Bloco de Notas** (`notepad 2identify_api\.env`).
Não use `Set-Content -Encoding utf8` do PowerShell 5.1 para editá-los (ver [6.19](#619-soluções-para-erros-comuns)).

### 6.9 Configurar a conexão com o banco e os segredos

**a) `2identify_api\.env`** — ajuste a linha do banco com a senha definida em 6.4:

```dotenv
DATABASE_URL=postgresql+psycopg2://identify_user:SUA_SENHA@localhost:5432/identify_db
```

Se a senha tiver caracteres especiais (`@`, `:`, `/`, `#`, `%`), codifique-os na URL (ex.: `@` → `%40`).

**b) Segredos gerados automaticamente** (nenhum valor é exibido):

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m scripts.ensure_auth_secret
.\.venv\Scripts\python.exe -m scripts.ensure_operator_catalog_token
```

O primeiro gera `AUTH_TOKEN_SECRET`; o segundo grava o mesmo `OPERATOR_CATALOG_TOKEN` na API e no Operador.

**c) Tokens que você define manualmente.** A API **não inicia** enquanto essas linhas contiverem
`CHANGE_ME...`. Gere um valor aleatório:

```powershell
.\.venv\Scripts\python.exe -c "import secrets; print(secrets.token_urlsafe(48))"
```

| Variável | Onde | O que fazer |
|---|---|---|
| `OPERATOR_STATION_TOKEN` | `2identify_api\.env` **e** `2identify_operador\.env` | Cole o **mesmo** valor gerado nos dois arquivos. Necessário para o Face ID. |
| `SAFETY_DEVICE_TOKEN` | `2identify_api\.env` | Cole outro valor gerado se for usar o ESP32; caso contrário deixe vazio: `SAFETY_DEVICE_TOKEN=` |

**d) `2identify_admin\.env`** — o Admin desktop só precisa de `API_URL=http://127.0.0.1:8000`
(já vem assim). Para rodar o `seed.py` do passo 6.11, preencha também `DATABASE_URL` com **a mesma
URL** usada na API.

**e) `2identify_operador\.env`** — `API_URL=http://localhost:8000` já vem configurado.

### 6.10 Inicializar o banco (migrações)

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m alembic upgrade head
.\.venv\Scripts\python.exe -m alembic current
```

Resultado esperado: `a3c5e7f9b1d2 (head)`. Aplique as migrações **somente a partir da API**: a pasta
do Admin não possui as duas revisões mais recentes.

### 6.11 Preparar dados e usuários de teste

**Administrador, setores, EPIs e funcionários de demonstração** (comando do projeto; altera o banco):

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
.\.venv\Scripts\python.exe seed.py
```

O script pede (sem exibir) a senha do usuário `admin` — mínimo de 12 caracteres — e cria:
usuário `admin` (perfil administrador), 4 setores, 6 EPIs (Capacete, Luvas, Botas, Mangote, Óculos,
Protetor auricular) e 3 funcionários fictícios. Nenhuma senha vem no repositório.

**Conta de operador para login por usuário/senha (opcional).** Não existe tela para criar contas
de operador; o bloco abaixo usa os modelos e o hash do próprio Admin. Execute na pasta
`2identify_admin`:

```powershell
@'
import getpass
from sqlalchemy import select
from app.core.database import SessionLocal
from app.models.usuario import Usuario
from seed import gerar_hash_senha

username = input("Usuario do operador (ex.: operador.demo): ").strip()
nome = input("Nome exibido: ").strip() or username
senha = getpass.getpass("Senha (minimo 12 caracteres, nao e exibida): ")
if len(senha) < 12:
    raise SystemExit("A senha deve ter pelo menos 12 caracteres.")
with SessionLocal() as session:
    if session.scalar(select(Usuario).where(Usuario.username == username)):
        raise SystemExit("Ja existe um usuario com esse nome.")
    session.add(Usuario(nome=nome, username=username, senha_hash=gerar_hash_senha(senha), perfil="operador", ativo=True))
    session.commit()
print("Conta de operador criada.")
'@ | .\.venv\Scripts\python.exe -
```

O login por **Face ID** não precisa dessa conta: ele usa os funcionários com rosto cadastrado no Admin.

### 6.12 Modelos de IA

| Modelo | Arquivo | Como obter |
|---|---|---|
| Detecção facial YuNet | `2identify_operador\models\face_detection_yunet_2023mar.onnx` | **Versionado** neste repositório (MIT). |
| Reconhecimento facial SFace | `2identify_operador\models\face_recognition_sface_2021dec.onnx` | **Versionado** (Apache 2.0). |
| Pose YOLO11n-pose | `2identify_operador\models\pose\yolo11n-pose.pt` | Baixado pelo script do projeto (verifica SHA-256). |
| Detector de EPI (YOLOv8 customizado, versão retreinada) | `2identify_operador\models\ppe\best.pt` | **Versionado** neste repositório (AGPL-3.0 declarada no checkpoint). |

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe scripts\download_face_models.py
.\.venv\Scripts\python.exe scripts\download_pose_model.py
```

O `best.pt` já vem no clone. O Operador só carrega o arquivo se o SHA-256 for igual a
`PPE_MODEL_SHA256` (no `.env` e no padrão do código), que nesta cópia é
`961AB27019BADAE6E2F952BD633E655EA1D58F708801A9A4B531CD39EB578B42`. Para conferir:

```powershell
(Get-FileHash .\models\ppe\best.pt -Algorithm SHA256).Hash
```

> Este checkpoint é a **versão retreinada** escolhida pelo grupo; o repositório de origem ainda
> referencia o hash da versão anterior (ver [Diferenças em relação à origem](#diferenças-em-relação-à-origem)).
> Se o arquivo faltar ou tiver outro hash, a aplicação inicia, mas a verificação de EPIs não
> conclui e a operação **não é liberada** (comportamento *fail-closed*). O checkpoint declara
> licença AGPL-3.0 (ver `2identify_operador/THIRD_PARTY_NOTICES.md`).

Valide a configuração do Operador sem abrir a interface (retorna código 0 quando está tudo certo):

```powershell
.\.venv\Scripts\python.exe main.py --check
```

### 6.13 Iniciar a FastAPI (terminal 1)

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --workers 1 --ws-max-size 65536
```

Em outro terminal, confira:

```powershell
Invoke-RestMethod http://127.0.0.1:8000/health
```

Esperado: `status = ok` e `database = connected`. Documentação interativa: <http://127.0.0.1:8000/docs>.
Mantenha **um único worker**: os canais em tempo real usam broker em memória.

### 6.14 Iniciar o Admin (terminal 2)

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
.\.venv\Scripts\python.exe main.py
```

Entre com o usuário `admin` e a senha definida no `seed.py`.

### 6.15 Iniciar o Operador (terminal 3)

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe main.py
```

### 6.16 Configurar câmeras locais ou IP

1. No Admin, abra **Operações e Áreas → cadastrar câmera**: informe nome, setor e fonte —
   `0` para a webcam principal, `1` para a segunda câmera USB, ou a URL completa
   `rtsp://usuario:senha@host:554/caminho` para câmera IP (a API mascara a senha nas respostas ao Admin).
2. Índices USB são próprios de cada computador. Para descobrir quais abrem nesta máquina:

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
.\.venv\Scripts\python.exe scripts\probe_usb_cameras.py
```

3. Para usar outro índice ou uma URL privada só nesta estação, adicione ao `2identify_operador\.env`
   a linha `CAMERA_SOURCE_<id>=<fonte>`, usando o ID real da câmera (ex.: `CAMERA_SOURCE_1=0`).
4. A câmera do login facial é `LOGIN_CAMERA_SOURCE` (padrão `0`).

### 6.17 Testar o fluxo principal

1. **Admin → Operações e Áreas:** cadastre a câmera, clique em configurar área de risco, desenhe o
   polígono (mínimo 3 pontos) sobre a imagem capturada e salve.
2. **Admin → Operações e Áreas:** crie uma operação, selecione os EPIs obrigatórios (ex.: Capacete)
   e a área de risco.
3. **Admin → Funcionários:** selecione um funcionário e use **Capturar Face ID** (capturas frontal, esquerda e direita com a câmera do computador do Admin).
4. **Operador:** faça login por Face ID (ou com a conta de operador do passo 6.11).
5. **Operador → Operações:** selecione a operação e clique em **Começar trabalho**. A verificação de
   segurança mostra o estado de cada EPI; com todos confirmados, a operação inicia.
6. **Operação ativa:** retire um EPI, incline o tronco ou entre na área de risco. O alerta aparece
   no Operador e chega ao Admin em tempo real (banner, contador e **Alertas**).
7. **Admin → Alertas:** confirme e depois encerre a ocorrência. **Relatórios** exporta o histórico em Excel.

Sem câmera, é possível verificar a API e o Admin (login, cadastros, dashboard, alertas) e o login
do Operador por usuário/senha.

### 6.18 Executar os testes automatizados

Os testes usam bancos e transportes falsos: **não precisam** de PostgreSQL nem de câmera.

```powershell
Set-Location C:\dev\metaindustria-seguranca-ia\2identify_api
.\.venv\Scripts\python.exe -m pytest

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_admin
$env:QT_QPA_PLATFORM = "offscreen"
.\.venv\Scripts\python.exe -m pytest

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_operador
$env:QT_QPA_PLATFORM = "offscreen"
.\.venv\Scripts\python.exe -m pytest

Set-Location C:\dev\metaindustria-seguranca-ia\2identify_web
node --test
```

Resultado da validação desta entrega: API **219 aprovados** e Admin **142 aprovados** (22/10/2026);
Operador **510 aprovados, 0 ignorados** (23/10/2026, já com o `best.pt` — o teste do checkpoint
carrega o modelo e confere as 11 classes); Web **10 aprovados**. `ruff check .` sem apontamentos nos
três módulos Python.

### 6.19 Soluções para erros comuns

| Sintoma | Causa e solução |
|---|---|
| `Activate.ps1 não pode ser carregado` | Política de execução. Use `.\.venv\Scripts\python.exe` diretamente, como neste tutorial. |
| `ImportError: DLL load failed ... nome do arquivo ou a extensão é muito grande` ou erro de caminho longo no `pip` | Caminho do projeto longo demais. Clone em `C:\dev\...` e recrie a `.venv`. |
| API não inicia: `... ainda contém um placeholder` | `OPERATOR_STATION_TOKEN`, `SAFETY_DEVICE_TOKEN` ou `OPERATOR_CATALOG_TOKEN` com `CHANGE_ME`. Gere valores (6.9 c) ou deixe vazio. |
| API não inicia: `database_url Field required` com o `DATABASE_URL` preenchido | O `.env` foi salvo com BOM (ex.: `Set-Content -Encoding utf8` no PowerShell 5.1). Salve pelo Bloco de Notas como UTF-8. |
| `/health` retorna 503 | PostgreSQL parado, senha incorreta ou caracteres especiais sem codificação no `DATABASE_URL`. |
| Migração falha com `role "identify_user" does not exist` | Crie o role com esse nome exato (6.4). |
| `permission denied for schema public` | O banco não pertence a `identify_user`. Use `ALTER DATABASE identify_db OWNER TO identify_user;` como `postgres`. |
| Uvicorn não abre a porta 8000 (`[Errno 10048]`) | Outro processo usa a porta. Encerre-o; o Operador e o PoC web esperam a API na porta 8000. |
| Admin mostra erro de conexão | A API não está rodando ou `API_URL` está diferente de `http://127.0.0.1:8000`. |
| `main.py --check` retorna 1 com `pose_model_artifact_missing` | Rode `scripts\download_pose_model.py`. |
| Verificação de EPI nunca libera | `best.pt` ausente ou alterado (hash diferente de `PPE_MODEL_SHA256`), mais de uma pessoa na imagem, ou EPI sem classe correspondente no modelo. |
| Face ID: "Estação não autorizada na API: verifique OPERATOR_STATION_TOKEN." | `OPERATOR_STATION_TOKEN` diferente entre API e Operador, ou vazio. Reinicie a API e o Operador após corrigir. |
| Face ID: "Rosto não reconhecido ou funcionário não autorizado." | Rosto não cadastrado no Admin, funcionário inativo, iluminação ruim ou mais de um rosto na imagem. |
| Câmera não abre | Índice USB incorreto (use `probe_usb_cameras.py`), câmera em uso por outro programa ou URL RTSP inacessível. |

---

## 7. Evolução das Sprints

| Sprint | Foco | Principais entregas (conforme os artefatos do repositório) |
|---|---|---|
| Sprint 1 | Definição do problema e requisitos | Problema, proposta, 10 requisitos funcionais e 7 não funcionais, personas, restrições, tecnologias e justificativas. |
| Sprint 2 | Protótipo e arquitetura | Diagramas UML (casos de uso, atividades e classes) e protótipo no Figma (telas da Sprint 2 mantidas no Figma). |
| Sprint 3 | Interfaces, Scrum e documentação | Evolução do protótipo (fluxos Admin e Operador navegáveis), Product/Sprint Backlog no Trello, cerimônias Scrum e arquitetura técnica refinada. |
| Sprint 4 | Aplicação final e validação | Código do 2Identify (API, Admin, Operador, IA, ESP32, PoC web), instalação validada do zero, 881 testes automatizados aprovados, checkpoint de EPI retreinado, vídeo e documentação final. |

Comparação detalhada, inclusive requisitos atendidos: [docs/evolucao-sprints.md](docs/evolucao-sprints.md).

---

## 8. Documentação

### Sprint 4

- [Arquitetura final](docs/arquitetura-final.md)
- [Manual de execução](docs/manual-execucao.md)
- [Evolução das Sprints](docs/evolucao-sprints.md)
- [Sprint Review final — estrutura](docs/review-final.md)
- READMEs dos módulos: [API](2identify_api/README.md) · [Admin](2identify_admin/README.md) · [Operador](2identify_operador/README.md) · [Firmware ESP32](2identify_operador/firmware/esp32_safety_signal/README.md)
- Documentos técnicos do 2Identify: [guia multicâmera](docs/2identify/metaindustria-multicamera-field-guide.md) · [arquitetura do Operador](2identify_operador/docs/architecture.md) · [autenticação biométrica](2identify_operador/docs/biometric-authentication.md) · [integração Admin](2identify_api/docs/admin-integration.md) · [tempo real](2identify_api/docs/admin-realtime.md) · [integração Operador](2identify_api/docs/operator-integration.md)

### Sprint 3

- [Cerimônias Scrum — Sprint 3](docs/cerimonias-sprint3.md)
- [Arquitetura Técnica — Sprint 3](docs/arquitetura-sprint3.md)
- [README da Sprint 3 (versão integral preservada)](docs/historico/README-sprint3.md)

### Sprints 1 e 2

- [Requisitos](docs/requisitos.md)
- [Personas](docs/personas.md)
- [Restrições](docs/restricoes.md)
- [Diagrama de Casos de Uso](diagramas/casos-de-uso.md)
- [Diagrama de Atividades](diagramas/atividades.md)
- [Diagrama de Classes](diagramas/classes.md)

### Diferenças em relação à origem

- Copiados byte a byte os 490 arquivos versionados de `2identify_admin`, `2identify_api`,
  `2identify_operador` e `2identify_web` (branch `redesign/fase2-design-system`, commit `36252b8`).
- Copiados para `docs/2identify/` o guia multicâmera e o design/plano da gestão de EPI em tempo real.
- **Não** copiados: o README raiz da origem (substituído por este), e os relatórios de
  recuperação/apresentação de 16–17/09/2026 da pasta `docs/` da origem, que descrevem a máquina de um
  integrante (caminhos locais, backup do banco, estado do Git naquele dia).
- Os READMEs internos da API, do Admin e do Operador receberam correções de trechos desatualizados,
  identificadas no início de cada um.
- **Checkpoint de EPI (23/10/2026):** a cópia acadêmica versiona o `best.pt` **retreinado** escolhido
  pelo grupo (SHA-256 `961AB27019BADAE6E2F952BD633E655EA1D58F708801A9A4B531CD39EB578B42`, 6.254.698 bytes), ausente na origem.
  Para que ele seja aceito, `PPE_MODEL_SHA256` foi atualizado em `2identify_operador/.env.example`
  **e** no valor padrão de `2identify_operador/app/core/config.py` (única alteração de código-fonte
  em relação à origem, que ainda referencia `2EBD001C…F011F`). Também foram ajustados o `.gitignore`
  raiz e o do Operador (exceção para `models/ppe/best.pt`), o `.gitattributes` do Operador
  (`*.pt binary`) e o `THIRD_PARTY_NOTICES.md`. O modelo foi validado: carrega pelo adaptador do
  projeto, expõe as 11 classes esperadas (`bota`, `capacete`, `colete_refletivo`, `luva`, `mangote`, `mao_sem_luva`, `mascara`, `oculos`, `protetor_headset`, `protetor_intra`, `tronco_sem_colete`) e cobre todas as classes do mapeamento de EPIs.

---

## 9. Links

| Item | Link |
|---|---|
| GitHub | <https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia> |
| Trello | <https://trello.com/b/hlOQ2wC8> |
| Figma | [Protótipo SafeVision — Figma](https://www.figma.com/design/1jOYFBKYzRu1shWKXjoNmk/Sem-t%C3%ADtulo?node-id=0-1&t=j9OCNoC0o5MRH8zr-1) |
| Código de origem (2Identify) | <https://github.com/LMedeiros-Silva/2identify/tree/redesign/fase2-design-system> |
| Vídeo de apresentação — Sprint 4 | <https://youtu.be/2VN0RzsWvMM> |

---

## 10. Status da entrega — Sprint 4

Marcado somente o que foi realizado e verificado nesta entrega.

- [x] Código do 2Identify copiado para o repositório acadêmico, idêntico à origem (490 arquivos comparados)
- [x] Documentação das Sprints 1 a 3 preservada
- [x] README principal atualizado para a Sprint 4
- [x] Arquitetura final, manual de execução, evolução das Sprints e estrutura da Review documentados
- [x] Instalação validada em ambiente limpo: dependências, `.env`, migrações do zero e `seed.py`
- [x] Testes automatizados executados: 881 aprovados, 0 ignorados, 0 falhas
- [x] API iniciada com PostgreSQL; fluxo REST validado (login, câmera, área de risco, operação, alerta idempotente, confirmação e encerramento) e WebSocket do Admin
- [x] Admin e Operador iniciados no ambiente de validação; `main.py --check` do Operador aprovado
- [x] Vídeo de apresentação da Sprint 4 publicado e link adicionado
- [x] Checkpoint de EPI retreinado (`best.pt`) versionado e carregado com as classes esperadas
- [ ] Teste físico com câmera real (Face ID, EPI, pose, área de risco) registrado como evidência
- [ ] Teste físico da torre ESP32 registrado como evidência
- [ ] Sprint Review final realizada e registrada em [docs/review-final.md](docs/review-final.md)
- [ ] Board do Trello atualizado para a Sprint 4 (não verificado nesta entrega)
