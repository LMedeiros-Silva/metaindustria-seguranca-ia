# Sprint Review Final — Sprint 4 | Entrega Final

> **Status da ata: preparada para validação e registro da cerimônia.** Conteúdo fornecido pelo
> grupo em 23/10/2026. Data, demais participantes, feedback e aceitação formal das entregas
> ainda serão registrados após a realização da Review. A mesma ata está no card
> "Realizar a Sprint Review final e registrar a ata" do [board Trello](https://trello.com/b/hlOQ2wC8).

## 1. Objetivo da revisão

Apresentar a versão final do SafeVision Metaindústria — 2Identify, demonstrando a evolução da
solução ao longo das quatro Sprints e os resultados obtidos com a integração entre software,
inteligência artificial e hardware.

| Campo | Preenchimento |
|---|---|
| Data | A confirmar |
| Participantes | Arthur Bergamaço Alves (confirmado); demais a registrar |
| Responsável pela condução | Arthur Bergamaço Alves |
| Versão apresentada | branch `main` de <https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia> |

## Atividades desenvolvidas pelo grupo

| Integrante | Área | Atividades |
|---|---|---|
| Leonardo Medeiros | Software | Aplicação administrativa, aplicação do operador, API, integração com PostgreSQL e comunicação entre os componentes. |
| Arthur Bergamaço Alves | Inteligência Artificial | Desenvolvimento e aprimoramento da IA, modelos YOLO, detecção de EPIs e análise ergonômica. |
| Breno Barbosa Pereira | Design | Desenho e organização visual das interfaces da aplicação administrativa. |
| Mateus de Souza Santos | Hardware | Desenvolvimento e montagem do sistema físico de sinalização (ESP32 e torre luminosa). |
| Renan Melo Rosembeng | Hardware | Desenvolvimento e montagem do sistema físico de sinalização (ESP32 e torre luminosa). |
| Pietro Rodriguez | EPIs | Escolha dos EPIs utilizados no projeto e definição dos EPIs relevantes para o monitoramento industrial. |

## 2. Funcionalidades previstas para a Sprint 4

- Integração da aplicação final (Admin, Operador, API, IA, banco, ESP32) no repositório acadêmico.
- Autenticação (senha e Face ID) e cadastros (funcionários, câmeras, áreas de risco, operações).
- Verificação de EPIs antes da operação e monitoramento contínuo (EPI, ergonomia, área de risco).
- Alertas em tempo real, tratamento de ocorrências e relatórios.
- Tutorial de instalação e execução para Windows.
- Testes automatizados e documentação final.

## 3. Funcionalidades demonstradas

Resultados apresentados pelo grupo: sistema administrativo, sistema do operador com autenticação e
monitoramento, reconhecimento facial, detecção de EPIs por IA, monitoramento ergonômico,
identificação de situações de risco, geração e registro de alertas, comunicação pela API,
integração com PostgreSQL e sinalização com ESP32. O vídeo final demonstra o reconhecimento facial,
a verificação de EPIs e a identificação de risco ergonômico, e mostra a estrutura do hardware e a
aplicação administrativa.

Marque a coluna "Demonstrado na Review" durante a cerimônia. "Validado na preparação" indica o que já foi executado tecnicamente em
22/10/2026 (ver seção 4); não substitui a demonstração.

| Funcionalidade | Validado na preparação | Demonstrado na Review | Observações |
|---|---|---|---|
| Instalação a partir do README | Sim (banco vazio, Windows 11) | [ ] | |
| API com PostgreSQL (`/health`) | Sim | [ ] | |
| Login administrativo | Sim (HTTP e abertura do Admin) | [ ] | |
| Cadastro de câmera, área de risco e operação | Sim (HTTP) | [ ] | |
| Cadastro de Face ID no Admin | Não (exige câmera) | [ ] | |
| Login do Operador por senha | Sim (HTTP) | [ ] | |
| Login do Operador por Face ID | Não (exige câmera) | [ ] | Gravado no vídeo da Sprint 4, 1:44 |
| Verificação de EPIs e liberação da operação | Não (exige câmera); modelo carregado e classes conferidas | [ ] | Gravado no vídeo, 1:52–2:00 (capacete e colete) |
| Alerta de ergonomia / área de risco / queda | Não (exige câmera) | [ ] | Ergonomia gravada no vídeo, 2:41; área de risco e queda só explicadas |
| Alerta chegando ao Admin em tempo real | Parcial (WebSocket conectado; alerta via HTTP) | [ ] | |
| Confirmação e encerramento de alerta | Sim (HTTP) | [ ] | |
| Relatório Excel | Coberto por testes automatizados | [ ] | |
| Torre ESP32 | Não (exige hardware) | [ ] | Estrutura física mostrada no vídeo, 2:49; acionamento ao vivo não gravado |
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

### 4.3 Evidências em vídeo

Vídeo da Sprint 4: <https://youtu.be/2VN0RzsWvMM> (3min36s). Trechos com o sistema em execução, conferidos quadro a quadro
em 23/10/2026:

| Tempo | O que aparece |
|---|---|
| 1:44–1:52 | "Demonstração — Vídeo 1: entrada por Face ID": tela de login do Operador com câmera real e acesso à lista de operações |
| 1:52–2:05 | "Demonstração — Vídeo 2: verificação dos EPIs": câmera real com pessoa de capacete e colete e estado de cada EPI |
| 2:41–2:49 | Operação ativa com alerta de risco ergonômico (braço acima da linha do ombro) |
| 2:49–3:05 | Slide "Hardware de sinalização" com foto da torre montada e explicação de verde, amarelo e vermelho com buzina |
| 3:05–3:20 | Slide do Admin (central de segurança, alertas, relatórios e cadastros) |

Área de risco, queda e o acionamento da torre ao vivo aparecem apenas como explicação, sem
demonstração gravada.

### 4.4 Não executado

- Nesta validação técnica: câmera física, ESP32 e Supabase (o `best.pt` foi testado com quadros
  sintéticos; os testes físicos estão no vídeo, seção 4.3).
- Medições de precisão dos modelos e de latência.

## 5. Pendências e limitações

- Vídeo da Sprint 4 publicado: <https://youtu.be/2VN0RzsWvMM>.
- Checkpoint de EPI retreinado versionado em `2identify_operador/models/ppe/best.pt` (licença AGPL-3.0 declarada; uso comercial exige revisão).
- Evidência gravada de área de risco, queda e acionamento da torre ao vivo (não aparecem no vídeo).
- Limitações técnicas: brokers em memória (um worker), sem outbox durável, sem HTTPS/rate limiting,
  prova de vida *baseline*, triagem ergonômica não validada por especialista, sem cadastro de EPIs,
  setores e contas de operador pela interface, tela "Configurações" do Admin é placeholder.

## 6. Evolução ao longo das Sprints (apresentada na Review)

- **Sprint 1:** levantamento de requisitos, identificação do problema e definição da proposta de solução.
- **Sprint 2:** desenvolvimento do protótipo, definição das interfaces e elaboração dos diagramas.
- **Sprint 3:** evolução dos fluxos administrativos e operacionais, refinamento da arquitetura e
  organização das atividades com Scrum.
- **Sprint 4:** consolidação da aplicação funcional, integração dos componentes, testes,
  documentação final e apresentação do produto.

## 7. Feedback, decisões e pendências

**Feedback recebido:** pendente de registro após a realização da Review.

**Decisões e pendências:**

- A solução possui evidências técnicas de funcionamento, testes automatizados e documentação consolidada.
- A aceitação formal das entregas pelo grupo ainda precisa ser registrada.

| # | Tema | Decisão / feedback | Responsável | Prazo |
|---|---|---|---|---|
| 1 | Aceitação formal das entregas da Sprint 4 | Pendente | _a registrar_ | _a registrar_ |
| 2 | Feedback da Review | Pendente | _a registrar_ | _a registrar_ |

## 8. Oportunidades de melhoria (encaminhamentos)

- Ampliar os testes em ambiente industrial real.
- Realizar validações adicionais com o hardware físico.
- Aprimorar a precisão e o desempenho dos modelos de IA.
- Evoluir funcionalidades administrativas ainda parciais.
- Fortalecer os mecanismos de segurança para utilização em produção.

## 9. Conclusão

A Sprint 4 representa a consolidação do projeto SafeVision Metaindústria — 2Identify, reunindo os
principais componentes desenvolvidos ao longo das quatro Sprints. O resultado é uma solução
integrada voltada ao monitoramento de EPIs e à identificação de riscos industriais, com potencial
de contribuir para a prevenção de acidentes e para o acompanhamento das condições de segurança.

Vídeo final: <https://youtu.be/2VN0RzsWvMM> · Repositório:
<https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia>
