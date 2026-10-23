# Cerimônias Scrum — Sprint 4

> Registro elaborado com as informações fornecidas pelo grupo e com as evidências existentes no
> repositório e no [board Trello](https://trello.com/b/hlOQ2wC8). Itens marcados como
> **pendente de confirmação** ainda não foram confirmados pelo grupo e não devem ser lidos como
> cerimônias realizadas.

## 1. Objetivo da Sprint

Finalizar a solução SafeVision Metaindústria / 2Identify, consolidando uma aplicação funcional de
segurança industrial capaz de monitorar EPIs, identificar situações de risco, gerar alertas e
disponibilizar informações para gestores e operadores.

A Sprint também teve como objetivos integrar os componentes de software, inteligência artificial e
hardware, executar testes de validação, organizar a documentação final e demonstrar a evolução do
projeto ao longo das quatro Sprints.

---

## 2. Sprint Planning

**Data:** pendente de confirmação.

**Integrantes do grupo** (a presença na reunião ainda não foi confirmada):

* Leonardo Medeiros — RM 559220
* Arthur Bergamaço Alves — RM 556207
* Breno Barbosa Pereira — RM 555348
* Mateus de Souza Santos — RM 559118
* Pietro Rodriguez — RM 555899
* Renan Melo Rosembeng — RM 558535

### Objetivo definido para a Sprint

Finalizar a solução e integrar software, inteligência artificial e hardware, com testes de
validação, documentação final e demonstração da evolução do projeto (ver seção 1).

### Organização do trabalho

As atividades foram registradas no Trello, no mesmo board das Sprints anteriores. O histórico da
Sprint 3 foi preservado nas listas "Sprint Backlog — Sprint 3" e "Concluído — Sprint 3"; a Sprint 4
usa as listas "Sprint Backlog — Sprint 4", "Em revisão" e "Concluído — Sprint 4".

Fluxo do board: Product Backlog → Sprint Backlog → Em andamento → Em revisão → Concluído.

---

## 3. Participantes e responsabilidades

| Integrante | RM | Área | Atividades |
|---|---|---|---|
| Leonardo Medeiros | 559220 | Software | Aplicação administrativa; aplicação do operador; desenvolvimento e integração da API; integração com PostgreSQL; comunicação entre os componentes; implementação dos fluxos principais; organização do código e integração do software. |
| Arthur Bergamaço Alves | 556207 | Inteligência Artificial | Desenvolvimento e aprimoramento da IA; integração e uso dos modelos YOLO; detecção dos EPIs nas imagens; análise das condições de segurança por visão computacional; análises ergonômicas e estimativa de pose; validação do funcionamento dos modelos. Responsável pela condução da Sprint Review final. |
| Breno Barbosa Pereira | 555348 | Design do Admin (apoio) | Apoio no desenho das interfaces do Admin; colaboração na definição do layout administrativo; organização visual das telas; apoio na experiência de navegação; alinhamento visual entre protótipo e sistema. |
| Mateus de Souza Santos | 559118 | Hardware (com Renan) | Desenvolvimento da parte física do projeto; montagem do sistema de sinalização; uso do ESP32; organização dos circuitos e componentes eletrônicos; participação na integração do hardware. |
| Renan Melo Rosembeng | 558535 | Hardware (com Mateus) | Desenvolvimento e montagem dos componentes físicos; apoio na configuração do ESP32; montagem e integração da torre de sinalização; conexão entre os dispositivos; apoio aos testes físicos do hardware. |
| Pietro Rodriguez | 555899 | EPIs | Pesquisa e seleção dos EPIs do projeto; definição dos equipamentos relevantes ao cenário industrial; identificação dos EPIs a monitorar; apoio aos requisitos de segurança dos EPIs; colaboração na escolha dos equipamentos usados nos testes. |

Mateus e Renan trabalharam em conjunto no hardware, sem divisão rígida de atividades.

---

## 4. Sprint Backlog

Situação conforme o board Trello em 23/10/2026.

| # | Item | Responsáveis | Situação |
|---|---|---|---|
| 1 | Finalização do software administrativo | Leonardo Medeiros; colaboração no design: Breno Barbosa Pereira | Concluído |
| 2 | Finalização da aplicação do operador | Leonardo Medeiros | Concluído |
| 3 | Integração da API e banco de dados | Leonardo Medeiros | Concluído |
| 4 | Finalização da Inteligência Artificial | Arthur Bergamaço Alves | Concluído |
| 5 | Seleção e definição dos EPIs | Pietro Rodriguez | Concluído |
| 6 | Desenvolvimento do hardware de sinalização | Mateus de Souza Santos e Renan Melo Rosembeng | Concluído |
| 7 | Integração entre software, IA e hardware | Leonardo (software e API), Arthur (IA), Mateus e Renan (hardware) | Em revisão — falta evidência registrada do acionamento físico da torre em tempo real |
| 8 | Revisão visual da aplicação administrativa | Breno Barbosa Pereira e Leonardo Medeiros | Concluído |
| 9 | Testes e validação da solução | Leonardo (software e integrações), Arthur (IA), Mateus e Renan (hardware), Pietro (seleção dos EPIs) | Em revisão — 881 testes automatizados aprovados; testes físicos complementares do hardware pendentes |
| 10 | Consolidação do repositório acadêmico | Leonardo Medeiros (responsável sugerido, pendente de confirmação) | Concluído |
| 11 | Documentação da Sprint 4 | Responsável individual não confirmado | Concluído |
| 12 | Pitch e apresentação final | Pendentes de confirmação | Concluído (vídeo publicado) |
| 13 | Sprint Review e fechamento Scrum | Condução: Arthur Bergamaço Alves; documentação e Trello: pendente de confirmação | Sprint Backlog — aguardando a realização da Review |

O card "Atualizar README da Sprint 4", já existente no board, foi mantido em "Concluído — Sprint 4"
como parte da documentação (item 11).

---

## 5. Definition of Done — DoD

Mantidos os mesmos critérios da Sprint 3. Uma tarefa é considerada concluída quando:

1. Todos os requisitos definidos forem atendidos.
2. A implementação, documentação ou protótipo estiver finalizado.
3. O resultado tiver sido revisado por pelo menos um integrante do grupo.
4. Não existirem erros conhecidos que impeçam sua utilização.
5. As alterações relevantes estiverem documentadas.
6. O card correspondente estiver atualizado no Trello.
7. A atividade estiver na coluna "Concluído".

---

## 6. Daily Scrum

Não há registros de Dailies da Sprint 4 (datas, participantes ou atas) no board nem na
documentação. Por isso, nenhuma Daily é registrada para esta Sprint.

---

## 7. Sprint Review

**Data:** pendente de confirmação.

**Responsável pela condução:** Arthur Bergamaço Alves — RM 556207.

**Participante confirmado:** Arthur Bergamaço Alves. Demais participantes e participação de
professor ou mentor: pendentes de confirmação.

A ata, as evidências disponíveis (testes automatizados, validações de instalação e vídeo final) e
os campos ainda pendentes estão em [review-final.md](review-final.md). O feedback e a aceitação
formal das entregas serão registrados após a realização da Review.

---

## 8. Retrospectiva

**Status:** pendente de realização. Os tópicos abaixo são **sugestões para discussão** e não
representam pontos aprovados pelo grupo.

### Sugestões — o que funcionou bem

* Divisão das responsabilidades entre os integrantes.
* Desenvolvimento modular do software.
* Integração entre software e inteligência artificial.
* Participação dos responsáveis pelo hardware.
* Definição dos EPIs utilizados no projeto.
* Evolução do design administrativo.
* Avanço de um protótipo acadêmico para uma aplicação funcional.
* Cobertura de testes automatizados.
* Consolidação da documentação.

### Sugestões — o que pode melhorar

* Realizar acompanhamento mais frequente pelo Trello.
* Registrar decisões técnicas durante o desenvolvimento.
* Aumentar a frequência de testes integrados.
* Ampliar testes com hardware físico.
* Melhorar a rastreabilidade entre requisitos e funcionalidades.
* Registrar evidências de demonstração de todos os componentes.
* Reservar mais tempo para revisões intermediárias.
