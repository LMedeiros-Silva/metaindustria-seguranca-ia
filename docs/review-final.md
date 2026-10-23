# Sprint Review Final — Sprint 4 | Entrega Final da Solução

> **Status da ata: preparada para validação e registro da cerimônia.** A data, os demais
> participantes, o feedback e a aceitação formal das entregas ainda não foram confirmados e serão
> registrados após a realização da Review. A mesma ata está no card "Sprint Review e fechamento
> Scrum" do [board Trello](https://trello.com/b/hlOQ2wC8). Os artefatos Scrum da Sprint estão em
> [cerimonias-sprint4.md](cerimonias-sprint4.md).

## 1. Identificação

| Campo | Registro |
|---|---|
| Projeto | SafeVision Metaindústria / 2Identify |
| Sprint | 4 — Entrega Final da Solução |
| Data | Pendente de confirmação |
| Responsável pela condução | Arthur Bergamaço Alves — RM 556207 |
| Participante confirmado | Arthur Bergamaço Alves — RM 556207 |
| Demais participantes | Pendentes de confirmação |
| Professor ou mentor convidado | Participação não informada |
| Versão apresentada | branch `main` de <https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia> |

## 2. Objetivo da Review

Avaliar a solução desenvolvida pelo grupo, verificando a evolução dos requisitos e protótipos
iniciais até a aplicação integrada, além de analisar o funcionamento dos componentes, a
documentação e os resultados obtidos.

## 3. Resultados disponíveis para apresentação

### Software — Leonardo Medeiros (RM 559220)

- Aplicação administrativa.
- Aplicação do operador.
- Integração dos módulos.
- API FastAPI.
- Banco PostgreSQL.
- Comunicação entre aplicações.
- Autenticação.
- Gestão operacional.
- Registro e tratamento de alertas.

### Inteligência Artificial — Arthur Bergamaço Alves (RM 556207)

- Modelos YOLO aplicados à detecção de EPIs.
- Reconhecimento dos equipamentos de proteção.
- Estimativa de pose.
- Monitoramento ergonômico.
- Identificação de condições de risco.
- Integração dos resultados da IA com o software.

### Design administrativo — Breno Barbosa Pereira (RM 555348)

- Participação no desenho do software administrativo.
- Apoio à organização visual da aplicação.
- Colaboração na definição das interfaces.
- Evolução visual em relação ao protótipo.

### Hardware — Mateus de Souza Santos (RM 559118) e Renan Melo Rosembeng (RM 558535)

- Desenvolvimento e montagem do hardware.
- Utilização do ESP32.
- Estrutura de sinalização física.
- Integração dos dispositivos com a solução.

### EPIs — Pietro Rodriguez (RM 555899)

- Seleção dos EPIs utilizados no projeto.
- Definição dos equipamentos relevantes ao cenário.
- Apoio aos requisitos de monitoramento.

## 4. Evidências disponíveis

Ambiente da validação técnica: Windows 11 Pro, Python 3.12.10, PostgreSQL 18.6 em cluster isolado
criado para a validação, Node.js 24.19.0. Cópia do código idêntica à origem (490 arquivos
comparados byte a byte).

### 4.1 Testes automatizados

| Módulo | Comando | Resultado |
|---|---|---|
| `2identify_api` | `python -m pytest` | 219 aprovados |
| `2identify_admin` | `python -m pytest` (Qt offscreen) | 142 aprovados |
| `2identify_operador` | `python -m pytest` (Qt offscreen) | 510 aprovados, 0 ignorados (23/10/2026, com o `best.pt`) |
| `2identify_web` | `node --test` | 10 aprovados |
| **Total** | | **881 aprovados** |
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

### 4.3 Vídeo final

Vídeo: <https://youtu.be/2VN0RzsWvMM> (aproximadamente 3min36s). Evidências gravadas, conferidas
quadro a quadro em 23/10/2026:

| Tempo | Evidência |
|---|---|
| 1:44–1:52 | Reconhecimento facial do operador (login por Face ID com câmera real) |
| 1:52–2:05 | Verificação dos EPIs com câmera real (pessoa de capacete e colete) |
| 2:41–2:49 | Monitoramento da operação ativa com alerta de risco ergonômico |
| 2:49–3:05 | Estrutura do hardware de sinalização (foto da torre montada e explicação das cores) |
| 3:05–3:20 | Aplicação administrativa (central de segurança, alertas, relatórios e cadastros) |

O vídeo **não** registra o acionamento físico da torre em tempo real, a detecção de queda nem a
entrada em área de risco; esses recursos aparecem apenas como explicação.

### 4.4 Não executado na validação técnica

- Câmera física, ESP32 e Supabase (o `best.pt` foi testado com quadros sintéticos; os testes
  físicos gravados estão na seção 4.3).
- Medições de precisão dos modelos e de latência.

## 5. Funcionalidades demonstradas na Review

Marque durante a cerimônia.

| Funcionalidade | Evidência prévia | Demonstrado na Review |
|---|---|---|
| Instalação a partir do README | Validação técnica (seção 4.2) | [ ] |
| Login administrativo e central de segurança | Validação técnica; vídeo 3:05 | [ ] |
| Cadastro de câmera, área de risco e operação | Validação técnica (HTTP) | [ ] |
| Login do Operador por Face ID | Vídeo 1:44 | [ ] |
| Verificação de EPIs e liberação da operação | Vídeo 1:52 | [ ] |
| Alerta de ergonomia na operação ativa | Vídeo 2:41 | [ ] |
| Área de risco e detecção de queda | Sem demonstração gravada | [ ] |
| Alerta no Admin, confirmação e encerramento | Validação técnica (HTTP e WebSocket) | [ ] |
| Relatório Excel | Testes automatizados | [ ] |
| Torre ESP32 | Estrutura no vídeo 2:49; acionamento ao vivo não gravado | [ ] |

## 6. Feedback recebido

_Nenhum feedback formal foi informado até o momento. Registrar aqui o feedback real dos
participantes e mentores após a Review._

| Origem | Feedback |
|---|---|
| | |

## 7. Decisões

- Os resultados técnicos concluídos e documentados podem ser apresentados para aceitação durante a Review.
- A aceitação formal das entregas pelo grupo **ainda não foi registrada**.

| # | Tema | Decisão | Registro |
|---|---|---|---|
| 1 | Aceitação formal das entregas da Sprint 4 | _pendente_ | _a registrar após a Review_ |

## 8. Pendências e trabalhos futuros

Limitações identificadas no projeto (responsáveis e prazos não definidos):

- Validações adicionais em ambiente industrial real.
- Testes físicos complementares da torre de sinalização.
- Medições de desempenho e precisão da IA.
- Aprimoramento dos mecanismos de segurança e implantação (HTTPS, limitação de tentativas, outbox durável).
- Evolução das funcionalidades administrativas ainda parciais (tela "Configurações", cadastro de EPIs, setores e contas de operador pela interface).
- Evidência gravada de área de risco, queda e acionamento da torre ao vivo.

## 9. Evolução ao longo das Sprints

- **Sprint 1:** levantamento de requisitos, identificação do problema e definição da proposta de solução.
- **Sprint 2:** desenvolvimento do protótipo, definição das interfaces e elaboração dos diagramas.
- **Sprint 3:** evolução dos fluxos administrativos e operacionais, refinamento da arquitetura e
  organização das atividades com Scrum.
- **Sprint 4:** consolidação da aplicação funcional, integração dos componentes, testes,
  documentação final e apresentação do produto.

Detalhes: [evolucao-sprints.md](evolucao-sprints.md).

## 10. Campos pendentes de confirmação

- Data da Review.
- Demais participantes e participação de professor ou mentor.
- Feedback recebido.
- Aceitação formal das entregas.
- Responsável pela documentação e atualização do Trello no fechamento da Sprint.
