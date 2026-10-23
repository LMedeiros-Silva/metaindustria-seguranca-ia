# Sprint Review Final — Sprint 4 (estrutura)

> Este documento é uma **estrutura a ser preenchida pelo grupo**. As seções 4 e 5 trazem as
> evidências técnicas já coletadas na preparação da entrega. A realização da cerimônia, os
> participantes, as decisões e o feedback **ainda não ocorreram** e não foram registrados.

## 1. Objetivo da revisão

Apresentar a aplicação final SafeVision Metaindústria / 2Identify, verificar se o incremento da
Sprint 4 atende aos objetivos definidos (aplicação integrada, instalação reproduzível, testes e
documentação) e registrar decisões e pendências.

| Campo | Preenchimento |
|---|---|
| Data | _a definir_ |
| Participantes | _a preencher_ |
| Facilitador | _a preencher_ |
| Versão apresentada | branch `main` de <https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia> |

## 2. Funcionalidades previstas para a Sprint 4

- Integração da aplicação final (Admin, Operador, API, IA, banco, ESP32) no repositório acadêmico.
- Autenticação (senha e Face ID) e cadastros (funcionários, câmeras, áreas de risco, operações).
- Verificação de EPIs antes da operação e monitoramento contínuo (EPI, ergonomia, área de risco).
- Alertas em tempo real, tratamento de ocorrências e relatórios.
- Tutorial de instalação e execução para Windows.
- Testes automatizados e documentação final.

## 3. Funcionalidades demonstradas

Marque durante a Review. "Validado na preparação" indica o que já foi executado tecnicamente em
22/10/2026 (ver seção 4); não substitui a demonstração.

| Funcionalidade | Validado na preparação | Demonstrado na Review | Observações |
|---|---|---|---|
| Instalação a partir do README | Sim (banco vazio, Windows 11) | [ ] | |
| API com PostgreSQL (`/health`) | Sim | [ ] | |
| Login administrativo | Sim (HTTP e abertura do Admin) | [ ] | |
| Cadastro de câmera, área de risco e operação | Sim (HTTP) | [ ] | |
| Cadastro de Face ID no Admin | Não (exige câmera) | [ ] | |
| Login do Operador por senha | Sim (HTTP) | [ ] | |
| Login do Operador por Face ID | Não (exige câmera) | [ ] | |
| Verificação de EPIs e liberação da operação | Não (exige câmera); modelo carregado e classes conferidas | [ ] | |
| Alerta de ergonomia / área de risco / queda | Não (exige câmera) | [ ] | |
| Alerta chegando ao Admin em tempo real | Parcial (WebSocket conectado; alerta via HTTP) | [ ] | |
| Confirmação e encerramento de alerta | Sim (HTTP) | [ ] | |
| Relatório Excel | Coberto por testes automatizados | [ ] | |
| Torre ESP32 | Não (exige hardware) | [ ] | |
| PoC web no celular | Parcial (página, CORS, login inválido) | [ ] | |

## 4. Evidências de testes

Ambiente: Windows 11 Pro, Python 3.12.10, PostgreSQL 18.6 em cluster isolado criado para a
validação, Node.js 24.19.0. Cópia do código idêntica à origem (490 arquivos comparados byte a byte).

### 4.1 Testes automatizados

| Módulo | Comando | Resultado |
|---|---|---|
| `2identify_api` | `python -m pytest` | 219 aprovados |
| `2identify_admin` | `python -m pytest` (Qt offscreen) | 142 aprovados |
| `2identify_operador` | `python -m pytest` (Qt offscreen) | 510 aprovados, 0 ignorados (23/10/2026, com o `best.pt`) |
| `2identify_web` | `node --test` | 10 aprovados |
| Lint | `python -m ruff check .` nos três módulos Python | sem apontamentos |
| Dependências | `python -m pip check` nos três módulos | sem conflitos |

### 4.2 Validação de instalação e integração

| Verificação | Resultado |
|---|---|
| `alembic upgrade head` em banco vazio | 10 revisões aplicadas, head `a3c5e7f9b1d2` |
| `seed.py` | admin, 4 setores, 6 EPIs, 3 funcionários fictícios criados |
| Criação de conta de operador (bloco do README) | conta criada |
| `GET /health` | 200, banco conectado |
| `POST /auth/admin/login`, `GET /admin/me`, `GET /admin/dashboard/summary` | 200 |
| `POST /admin/cameras`, `/admin/risk-areas`, `/admin/operations` | 201 |
| `POST /auth/login` (operador) e `GET /operator/operations` | 200 |
| `POST /operator/alerts` (novo / repetido) | 201 / 200 com `duplicate=true` |
| Encerrar alerta antes de confirmar / confirmar / encerrar | 409 / 200 / 200 |
| `WS /ws/admin/alerts` com token / sem token | `connection.ready` / HTTP 401 |
| Admin `python main.py` | interface iniciada e mantida em execução |
| Operador `python main.py --check` | código 0 (após instalar o modelo de pose); `ppe_model_available: true` |
| Checkpoint de EPI retreinado | SHA-256 `961AB270…8B42`; carregado pelo adaptador do projeto; 11 classes; todas as classes do mapeamento presentes; ~35 ms/quadro 1280×720 em CPU |
| Operador `python main.py` | interface iniciada (`operator_ui_started`) |

### 4.3 Não executado

- Testes com câmera física, ESP32 e Supabase (o `best.pt` foi testado apenas com quadros sintéticos).
- Medições de precisão dos modelos e de latência.

## 5. Pendências e limitações

- Vídeo da Sprint 4 publicado: <https://youtu.be/2VN0RzsWvMM>.
- Checkpoint de EPI retreinado versionado em `2identify_operador/models/ppe/best.pt` (licença AGPL-3.0 declarada; uso comercial exige revisão).
- Registrar evidências do teste físico (câmera, Face ID, EPI, pose, área de risco, ESP32).
- Limitações técnicas: brokers em memória (um worker), sem outbox durável, sem HTTPS/rate limiting,
  prova de vida *baseline*, triagem ergonômica não validada por especialista, sem cadastro de EPIs,
  setores e contas de operador pela interface, tela "Configurações" do Admin é placeholder.

## 6. Decisões e feedback do grupo

_Preencher durante a Review._

| # | Tema | Decisão / feedback | Responsável | Prazo |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |

## 7. Encaminhamentos

_Preencher após a Review (itens que entram no backlog ou ficam como trabalhos futuros)._
