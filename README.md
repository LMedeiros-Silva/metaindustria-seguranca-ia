# SafeVision Metaindústria

## Integrantes

- Leonardo Medeiros — RM: 559220
- Arthur Bergamaço Alves — RM: 556207
- Breno Barbosa Pereira — RM: 555348
- Mateus de Souza Santos — RM: 559118
- Pietro Rodriguez — RM: 555899
- Renan Melo Rosembeng — RM: 558535

---

## Problema Abordado

Ambientes industriais apresentam riscos relacionados ao uso incorreto de EPIs, posturas inadequadas e aproximação de áreas perigosas.

Muitas vezes, a fiscalização depende apenas da supervisão humana, o que pode gerar atrasos na identificação de situações de risco.

---

## Proposta de Solução

O **SafeVision Metaindústria** é uma solução de segurança industrial proativa baseada em Inteligência Artificial e Visão Computacional.

O sistema utiliza câmeras para monitorar funcionários em tempo real, detectar EPIs, identificar posturas de risco e verificar a entrada em áreas perigosas cadastradas pelo usuário.

Quando um risco é identificado, o sistema gera alertas visuais na aplicação e poderá acionar dispositivos físicos, como Stack Lights industriais.

A solução é dividida entre uma aplicação administrativa e uma aplicação voltada ao operador, permitindo tanto o gerenciamento das informações quanto o acompanhamento das operações realizadas no ambiente industrial.

---

## Tecnologias Selecionadas

- Python
- YOLO
- SAM
- OpenCV
- PySide6
- FastAPI
- PostgreSQL
- Figma
- Trello
- GitHub
- Mermaid / Draw.io para UML

---

## Justificativa Técnica

**Python** foi escolhido por possuir grande suporte a bibliotecas de Inteligência Artificial, Visão Computacional e desenvolvimento de aplicações.

**YOLO** foi escolhido por permitir a detecção de objetos em tempo real, sendo utilizado na identificação dos Equipamentos de Proteção Individual.

**SAM** foi considerado como apoio para segmentação corporal e análises relacionadas ao ambiente e ao trabalhador.

**OpenCV** é utilizado para captura e processamento das imagens obtidas pelas câmeras.

**PySide6** permite o desenvolvimento das interfaces gráficas utilizadas pelas aplicações.

**FastAPI** é utilizado para estruturar a comunicação entre as aplicações e os serviços do sistema.

**PostgreSQL** é utilizado para armazenar informações como usuários, funcionários, EPIs, operações, alertas, áreas de risco, registros de conformidade e histórico de ocorrências.

---

# Sprint 3 — Evolução do Protótipo e Gestão Ágil com Scrum

Durante a Sprint 3, o projeto passou por uma evolução do protótipo desenvolvido nas etapas anteriores e por um refinamento da organização do trabalho utilizando práticas do framework Scrum.

O protótipo foi atualizado utilizando como referência a evolução real da aplicação, permitindo representar de forma mais fiel os fluxos administrativos e operacionais do sistema.

As telas desenvolvidas na Sprint 2 foram mantidas no Figma como referência, enquanto as novas telas da Sprint 3 demonstram a evolução da aplicação.

---

## Evolução do Protótipo

Na Sprint 3 foram adicionadas novas telas e novos fluxos que não estavam completamente contemplados na versão anterior.

O Figma foi organizado de forma a permitir a comparação entre:

- Protótipo desenvolvido na Sprint 2;
- Evolução realizada na Sprint 3;
- Aplicação administrativa;
- Aplicação do operador.

Além da inclusão das novas telas, foram criados fluxos navegáveis utilizando o modo Prototype do Figma.

---

### Evolução da Aplicação Administrativa

O fluxo administrativo foi ampliado para representar com maior fidelidade as funcionalidades disponíveis para os responsáveis pela gestão da segurança.

Entre as funcionalidades representadas estão:

- Login administrativo;
- Dashboard;
- Gestão de EPIs;
- Cadastro de funcionários;
- Cadastro de operações;
- Gerenciamento de áreas de risco;
- Central de alertas;
- Detalhamento de ocorrências;
- Relatórios.

Essas alterações foram realizadas para representar funcionalidades que não estavam totalmente contempladas no protótipo da Sprint anterior.

---

### Novo Fluxo do Operador

Durante a Sprint 3 também foi desenvolvido um fluxo específico para o operador.

O objetivo foi representar a jornada realizada pelo funcionário durante a utilização da aplicação no ambiente industrial.

O fluxo contempla:

```text
Login
  ↓
Identificação do Operador
  ↓
Operações Disponíveis
  ↓
Seleção da Operação
  ↓
Início da Operação
  ↓
Operação Ativa
  ↓
Monitoramento dos EPIs
```

Esse fluxo amplia significativamente o protótipo anterior, pois passa a representar não apenas funcionalidades administrativas, mas também a utilização prática da solução durante uma operação industrial.

---

## Justificativa das Alterações do Protótipo

As alterações realizadas durante a Sprint 3 foram motivadas pela evolução da aplicação e pela identificação de fluxos que ainda não estavam completamente representados na Sprint 2.

### Aplicação Administrativa

Foram incorporadas novas telas relacionadas ao gerenciamento de funcionários, operações, EPIs, áreas de risco e alertas.

Essas funcionalidades aumentam a capacidade de gerenciamento da plataforma e tornam o fluxo administrativo mais completo.

### Aplicação do Operador

Foi criado um fluxo dedicado ao operador para representar etapas como autenticação, seleção da operação, início da atividade e acompanhamento da operação ativa.

### Monitoramento

A evolução também permitiu representar com maior fidelidade o processo de monitoramento realizado durante uma operação, aproximando o protótipo da aplicação funcional desenvolvida pelo grupo.

### Usabilidade

Os novos fluxos foram organizados de forma a facilitar a compreensão da navegação e separar claramente as funcionalidades administrativas das funcionalidades utilizadas pelo operador.

---

# Protótipo Atualizado — Figma

O protótipo da Sprint 3 apresenta tanto as telas desenvolvidas anteriormente quanto a evolução realizada com base na aplicação atual.

Foram mantidas as telas da Sprint 2 para permitir a comparação visual com a evolução do projeto.

Também foram configurados fluxos navegáveis para demonstrar as principais jornadas do usuário.

**Link do protótipo:**

[Protótipo SafeVision — Figma](https://www.figma.com/design/1jOYFBKYzRu1shWKXjoNmk/Sem-t%C3%ADtulo?node-id=0-1&t=j9OCNoC0o5MRH8zr-1)

---

# Gestão Ágil — Scrum

Durante a Sprint 3, o grupo utilizou práticas do framework **Scrum** para organizar, acompanhar e documentar o desenvolvimento.

O Trello foi utilizado como ferramenta de gestão visual das atividades.

O board foi organizado utilizando as seguintes colunas:

```text
Product Backlog
      ↓
Sprint Backlog
      ↓
Em andamento
      ↓
Em revisão
      ↓
Concluído
```

Os cards representam atividades reais relacionadas ao desenvolvimento da solução.

Cada tarefa possui descrição, responsável e status atualizado de acordo com seu andamento durante a Sprint.

---

## Product Backlog

O Product Backlog reúne as funcionalidades e melhorias relacionadas ao projeto, incluindo:

- Autenticação de usuários;
- Autenticação por FaceID;
- Dashboard administrativo;
- Gestão de EPIs;
- Cadastro de funcionários;
- Cadastro de operações;
- Cadastro de áreas de risco;
- Central de alertas;
- Detalhamento de alertas;
- Relatórios;
- Monitoramento por câmera;
- Detecção de EPIs utilizando Inteligência Artificial;
- Início de operação pelo operador;
- Acompanhamento de operação ativa;
- Histórico de ocorrências;
- Evolução do protótipo;
- Refinamento da arquitetura;
- Documentação Scrum;
- Atualização da documentação do projeto.

---

## Sprint Backlog — Sprint 3

Durante o planejamento da Sprint 3 foram selecionadas atividades relacionadas principalmente à evolução do protótipo, organização Scrum e documentação.

Entre as atividades realizadas estão:

- Evolução do protótipo no Figma;
- Inclusão das telas reais da aplicação administrativa;
- Inclusão do fluxo do operador;
- Criação dos fluxos navegáveis;
- Organização do Product Backlog;
- Organização do Sprint Backlog;
- Refinamento da arquitetura técnica;
- Documentação das cerimônias Scrum;
- Atualização do README;
- Revisão dos artefatos da entrega.

---

## Board Trello

O board completo da Sprint 3 pode ser acessado pelo link abaixo:

[Board Scrum — Sprint 3](https://trello.com/b/hlOQ2wC8)

---

# Cerimônias Scrum

Durante a Sprint 3 foram registradas as principais cerimônias utilizadas pelo grupo.

A documentação contempla:

- Sprint Planning;
- Dailies;
- Sprint Review;
- Definition of Done — DoD.

---

## Sprint Planning

Durante a Planning foram definidos os objetivos da Sprint, as tarefas que seriam realizadas e a divisão das responsabilidades entre os integrantes.

Os principais objetivos definidos foram:

- Evoluir o protótipo desenvolvido na Sprint anterior;
- Adicionar novas telas e fluxos;
- Organizar o Product Backlog;
- Definir o Sprint Backlog;
- Atualizar o board do Trello;
- Refinar a arquitetura técnica;
- Documentar as cerimônias Scrum;
- Atualizar a documentação do projeto.

---

## Daily Scrum

As Dailies foram utilizadas para registrar o progresso das atividades, próximos passos e possíveis impedimentos encontrados durante a Sprint.

Os registros foram realizados de forma assíncrona, permitindo que os integrantes acompanhassem a evolução das atividades mesmo sem reuniões presenciais diárias.

---

## Sprint Review

Ao final da Sprint foi realizada uma revisão das entregas.

Foram verificados:

- Evolução do protótipo;
- Novas telas e fluxos;
- Organização do Trello;
- Product Backlog;
- Sprint Backlog;
- Arquitetura técnica;
- Documentação Scrum;
- README;
- Links utilizados na entrega.

---

## Definition of Done

Uma atividade é considerada concluída quando:

- Os requisitos definidos foram atendidos;
- A atividade foi finalizada;
- O resultado foi revisado;
- A documentação necessária foi atualizada;
- O card correspondente está atualizado no Trello;
- A tarefa foi movida para a coluna **Concluído**.

A documentação completa está disponível em:

[Cerimônias Scrum — Sprint 3](docs/cerimonias-sprint3.md)

---

# Arquitetura Técnica — Sprint 3

Durante a Sprint 3, a arquitetura da solução foi refinada para representar de forma mais clara a separação das responsabilidades entre os diferentes componentes da plataforma.

A solução foi dividida principalmente nos seguintes módulos:

- Aplicação Administrativa;
- Aplicação do Operador;
- Inteligência Artificial e Visão Computacional;
- API;
- Banco de Dados.

---

## Aplicação Administrativa

A aplicação administrativa é responsável pelas funcionalidades de gerenciamento e acompanhamento da plataforma.

Entre suas principais responsabilidades estão:

- Dashboard;
- Gestão de usuários;
- Gestão de funcionários;
- Gestão de EPIs;
- Gestão de operações;
- Gestão de áreas de risco;
- Central de alertas;
- Relatórios;
- Consulta de ocorrências.

---

## Aplicação do Operador

A aplicação do operador é responsável pela interação do trabalhador com a solução durante uma operação.

Entre suas principais funcionalidades estão:

- Autenticação;
- Identificação do operador;
- Seleção de operações disponíveis;
- Início da operação;
- Acompanhamento da operação ativa;
- Monitoramento relacionado ao uso dos EPIs.

---

## Inteligência Artificial e Visão Computacional

O módulo de Inteligência Artificial é responsável por analisar as imagens capturadas pelas câmeras.

A utilização de **YOLO** e **OpenCV** permite identificar equipamentos de proteção e auxiliar na identificação de situações que possam representar riscos durante uma operação.

O sistema pode identificar diferentes equipamentos e condições relacionadas à segurança, permitindo gerar alertas quando uma não conformidade é detectada.

---

## API

A API funciona como camada intermediária entre os diferentes componentes da solução.

Entre suas responsabilidades estão:

- Autenticação;
- Comunicação com as aplicações;
- Recebimento de eventos;
- Registro de ocorrências;
- Consulta de informações;
- Comunicação com o banco de dados.

A API é estruturada utilizando **FastAPI**.

---

## Banco de Dados

O banco de dados é responsável pelo armazenamento das informações utilizadas pelo sistema.

Entre os dados armazenados estão:

- Usuários;
- Funcionários;
- EPIs;
- Operações;
- Alertas;
- Áreas de risco;
- Ocorrências;
- Histórico das análises.

O banco de dados utilizado na arquitetura da solução é o **PostgreSQL**.

---

## Fluxo Simplificado da Arquitetura

```text
                    CÂMERA
                       │
                       ▼
              APLICAÇÃO OPERADOR
                       │
                       ▼
             INTELIGÊNCIA ARTIFICIAL
                YOLO + OpenCV
                       │
                       ▼
                     API
                  FastAPI
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
        PostgreSQL       Aplicação Admin
                             │
                             ▼
                 Dashboard / Alertas /
                  Gestão / Relatórios
```

---

## Evolução da Arquitetura

Nas Sprints anteriores, a arquitetura estava concentrada principalmente na definição conceitual da solução e das tecnologias que seriam utilizadas.

Durante a Sprint 3, a arquitetura foi refinada para representar de maneira mais clara os componentes reais da aplicação.

Foram definidos de forma mais clara:

- Aplicação do operador;
- Aplicação administrativa;
- Camada de Inteligência Artificial;
- API;
- Banco de dados;
- Fluxo de geração de alertas;
- Comunicação entre os diferentes componentes.

Esse refinamento melhora a separação de responsabilidades e facilita a manutenção e evolução futura da aplicação.

A documentação detalhada pode ser encontrada em:

[Arquitetura Técnica — Sprint 3](docs/arquitetura-sprint3.md)

---

# Documentação

## Documentação das Sprints Anteriores

- [Requisitos](docs/requisitos.md)
- [Personas](docs/personas.md)
- [Restrições](docs/restricoes.md)
- [Diagrama de Casos de Uso](diagramas/casos-de-uso.md)
- [Diagrama de Atividades](diagramas/atividades.md)
- [Diagrama de Classes](diagramas/classes.md)

## Documentação da Sprint 3

- [Cerimônias Scrum — Sprint 3](docs/cerimonias-sprint3.md)
- [Arquitetura Técnica — Sprint 3](docs/arquitetura-sprint3.md)

---

# Links da Sprint 3

## GitHub

https://github.com/LMedeiros-Silva/metaindustria-seguranca-ia

## Trello

https://trello.com/b/hlOQ2wC8

## Figma

https://www.figma.com/design/1jOYFBKYzRu1shWKXjoNmk/Sem-t%C3%ADtulo?node-id=0-1&t=j9OCNoC0o5MRH8zr-1

---

# Status da Sprint 3

- [x] Evolução do protótipo
- [x] Novas telas
- [x] Fluxo administrativo
- [x] Fluxo do operador
- [x] Fluxos navegáveis no Figma
- [x] Product Backlog
- [x] Sprint Backlog
- [x] Board Scrum no Trello
- [x] Responsáveis definidos nos cards
- [x] Cerimônias Scrum documentadas
- [x] Definition of Done
- [x] Refinamento da arquitetura
- [x] Atualização do README
- [x] Revisão dos artefatos da Sprint