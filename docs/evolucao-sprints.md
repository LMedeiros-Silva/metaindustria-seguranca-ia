# Evolução das Sprints — Sprint 1 à Sprint 4

Comparação construída a partir dos artefatos versionados neste repositório (documentos, diagramas
e histórico de commits) e do código copiado na Sprint 4. Quando um artefato não indica a Sprint a
que pertence, isso é informado.

## Linha do tempo pelo histórico do Git

| Data do commit | Artefato |
|---|---|
| 19/05/2026 | README inicial, requisitos, personas, restrições, diagramas de casos de uso, atividades e classes |
| 15/06/2026 | Atualização do diagrama de casos de uso |
| 21/09/2026 | Entrega da Sprint 3 (README, cerimônias Scrum, arquitetura técnica) e link público do Trello |
| 22/10/2026 | Sprint 4: cópia do código do 2Identify e documentação final |

Os documentos das primeiras entregas não registram explicitamente a qual Sprint (1 ou 2)
pertence cada arquivo; a divisão abaixo segue o foco de cada Sprint e o README da Sprint 3, que
cita as "telas desenvolvidas na Sprint 2" mantidas no Figma.

## Sprint 1 — Definição do problema e requisitos

- Problema: riscos por uso incorreto de EPIs, posturas inadequadas e aproximação de áreas
  perigosas, com fiscalização dependente de supervisão humana.
- Proposta: monitoramento por câmeras com IA e alertas visuais, com aplicação administrativa e
  aplicação do operador.
- [Requisitos](requisitos.md): RF01–RF10 e RNF01–RNF07.
- [Personas](personas.md): operador de chão de fábrica, supervisor de segurança, gestor industrial.
- [Restrições](restricoes.md): dependência de câmeras e iluminação, precisão do treinamento,
  ambiente controlado, hardware industrial em etapas futuras.
- Tecnologias selecionadas: Python, YOLO, SAM, OpenCV, PySide6, FastAPI e PostgreSQL.

## Sprint 2 — Protótipo e arquitetura

- Diagramas UML: [casos de uso](../diagramas/casos-de-uso.md), [atividades](../diagramas/atividades.md)
  e [classes](../diagramas/classes.md) (imagens hospedadas no GitHub).
- Primeiras telas do protótipo no Figma (preservadas no mesmo arquivo do Figma para comparação).

## Sprint 3 — Interfaces, Scrum e documentação

- Protótipo evoluído com base na aplicação real: fluxos administrativo (login, dashboard, EPIs,
  funcionários, operações, áreas de risco, alertas, ocorrências, relatórios) e do operador
  (login → identificação → operações → início → operação ativa → monitoramento de EPIs),
  navegáveis no Figma.
- Scrum: Product Backlog, Sprint Backlog e board no Trello; Planning (17/09/2026), três Dailies,
  Review (21/09/2026), Retrospectiva e Definition of Done — ver [cerimonias-sprint3.md](cerimonias-sprint3.md).
- Arquitetura em cinco blocos (Operador, Admin, IA, API, Banco) — ver [arquitetura-sprint3.md](arquitetura-sprint3.md).
- README integral da Sprint 3 preservado em [historico/README-sprint3.md](historico/README-sprint3.md).

## Sprint 4 — Aplicação final, integração, validação e documentação

- Código do 2Identify incorporado ao repositório: `2identify_api`, `2identify_admin`,
  `2identify_operador` (com firmware ESP32) e `2identify_web`.
- Integração ponta a ponta pela API: REST para cadastros, autenticação, operações e alertas;
  WebSocket para o Admin e para a torre ESP32.
- Validação: instalação do zero (dependências, migrações, seed), 881 testes automatizados
  aprovados (inclusive o do checkpoint de EPI retreinado), smoke test HTTP/WebSocket com PostgreSQL real
  e inicialização dos três aplicativos — ver [review-final.md](review-final.md).
- Documentação: [README](../README.md), [arquitetura final](arquitetura-final.md) e
  [manual de execução](manual-execucao.md).

## Comparação: o que foi planejado × o que foi implementado

| Aspecto | Sprints 1–3 (planejado / prototipado) | Sprint 4 (implementado) |
|---|---|---|
| Arquitetura | Operador, Admin, IA, API e Banco como blocos conceituais | Mesmos blocos, com a API como única fronteira com o banco e IA executada na estação |
| Detecção de EPIs | YOLO para capacete, luvas, botas, colete, mangote, máscara, óculos e protetores | YOLOv8 customizado (11 classes) com estabilização temporal, fail-closed, tracking e fusão multicâmera |
| Ergonomia | "Identificar posturas de risco" | YOLO11n-pose + regras de tronco, braços e joelhos; triagem de queda |
| Áreas de risco | Cadastro desenhando na imagem | Editor de polígono no Admin; detecção por keypoints da pose no Operador |
| Identificação do operador | FaceID no backlog e no fluxo do protótipo | Face ID com YuNet/SFace, prova de vida *baseline* e decisão na API; login por senha alternativo |
| Alertas | Alertas visuais e possível Stack Light | Alertas deduplicados, persistidos, em tempo real no Admin e na torre ESP32 |
| Ocorrências e relatórios | Central de alertas, detalhamento, relatórios | Histórico, confirmação, encerramento, resolução física e Excel |
| SAM | Considerado para segmentação | Não adotado |
| Hardware industrial | Planejado para etapas futuras | Firmware ESP32 com relés implementado (sem teste físico registrado nesta entrega) |

## Rastreabilidade dos requisitos (Sprint 1)

Legenda: ✅ atendido · 🟡 parcial · 🧪 implementado, sem validação física/medição nesta entrega.

| ID | Requisito | Situação | Evidência |
|---|---|---|---|
| RF01 | Cadastro de usuários | 🟡 | Funcionários cadastrados no Admin; contas `admin` e de operador criadas por `seed.py`/script, sem tela própria. |
| RF02 | Login de usuários autorizados | ✅ | `/auth/admin/login`, `/auth/login`, `/auth/face`; testes da API. |
| RF03 | Áreas de risco desenhadas na imagem | ✅ | Editor de polígono no Admin; `POST /admin/risk-areas` validado no smoke test. |
| RF04 | Detectar EPIs em tempo real | 🧪 | Pipeline YOLO implementado; checkpoint retreinado versionado, carregado e executado em quadros sintéticos (~35 ms/quadro em CPU); sem teste com câmera. |
| RF05 | Ausência de capacete, bota ou mangote | 🧪 | O checkpoint possui as classes `capacete`, `bota` e `mangote`, mapeadas aos EPIs do seed; a ausência bloqueia a liberação (fail-closed). Sem teste com câmera. |
| RF06 | Postura ergonômica inadequada | 🧪 | `ErgonomicsEngine` e worker de pose; limiares a validar por especialista. |
| RF07 | Entrada em área de risco | 🧪 | `RiskAreaPoseEngine` integrado ao monitoramento ativo. |
| RF08 | Alerta visual | ✅ | Alertas no Operador, banner/contador no Admin, torre ESP32. |
| RF09 | Registrar ocorrências | ✅ | `ocorrencias`/`alertas` com idempotência; validado no smoke test. |
| RF10 | Relatório de conformidade | 🟡 | Excel de alertas, ocorrências e EPIs com filtros; não há relatório de conformidade por operação/duração. |
| RNF01 | Processamento em tempo real | 🧪 | Inferência local configurada a ~4 FPS; não medido. |
| RNF02 | Baixa latência de alertas | 🧪 | Publicação após commit via WebSocket; latência não medida. |
| RNF03 | Interface simples e operacional | ✅ | Telas do Admin e do Operador alinhadas ao protótipo. |
| RNF04 | Armazenamento seguro | 🟡 | bcrypt, JWT curto, segredos fora do Git, senhas de câmera mascaradas; sem HTTPS/rate limiting. |
| RNF05 | Escalável para múltiplas câmeras | 🧪 | Multicâmera implementado; capacidade não medida. |
| RNF06 | Ambiente industrial controlado | ✅ | Projeto pensado para rede local; documentado nas restrições. |
| RNF07 | Manutenção e evolução dos modelos | ✅ | Caminhos, limiares e SHA-256 dos modelos configuráveis no `.env`. |
