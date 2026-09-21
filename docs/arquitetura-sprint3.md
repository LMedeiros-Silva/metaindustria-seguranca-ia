# Arquitetura Técnica — Sprint 3

## 1. Visão Geral

O SafeVision Metaindústria é uma solução voltada para segurança em ambientes industriais utilizando Inteligência Artificial e Visão Computacional.

A aplicação tem como objetivo monitorar trabalhadores, identificar situações de risco e gerar alertas relacionados principalmente ao uso de Equipamentos de Proteção Individual (EPIs).

Durante a Sprint 3, a arquitetura foi refinada para representar de maneira mais clara a separação entre interface do usuário, processamento, Inteligência Artificial, API e persistência dos dados.

---

# 2. Componentes da Solução

## Aplicação do Operador

A aplicação do operador é responsável pelo monitoramento realizado no ambiente industrial.

Entre suas responsabilidades estão:

* Captura de imagens da câmera;
* Monitoramento do trabalhador;
* Identificação do funcionário;
* Detecção de EPIs;
* Identificação de situações de risco;
* Envio das ocorrências para a API.

A interface pode ser implementada utilizando Python e PySide6.

---

## Aplicação Administrativa

A aplicação administrativa permite que responsáveis pela segurança acompanhem os dados coletados pelo sistema.

Entre as principais funcionalidades estão:

* Dashboard;
* Gestão de usuários;
* Gestão de EPIs;
* Visualização de alertas;
* Consulta de ocorrências;
* Relatórios;
* Configurações da aplicação.

---

# 3. Inteligência Artificial e Visão Computacional

O módulo de Inteligência Artificial é responsável pela análise das imagens capturadas pelas câmeras.

São utilizadas tecnologias como:

* Python;
* YOLO;
* OpenCV;
* Modelos de visão computacional.

O modelo YOLO é utilizado para detectar os equipamentos de proteção presentes na imagem.

Entre os itens que podem ser identificados estão:

* Capacete;
* Luvas;
* Botas;
* Colete refletivo;
* Mangote;
* Máscara;
* Óculos de proteção;
* Protetores auditivos.

A ausência de um EPI obrigatório pode gerar uma ocorrência no sistema.

---

# 4. API

A API funciona como camada intermediária entre as aplicações clientes e o banco de dados.

Entre suas responsabilidades estão:

* Autenticação;
* Controle de usuários;
* Recebimento dos eventos detectados;
* Registro de alertas;
* Consulta de informações;
* Comunicação entre os diferentes módulos da solução.

A API pode ser implementada utilizando FastAPI.

---

# 5. Banco de Dados

O banco de dados é responsável por armazenar as informações utilizadas pela plataforma.

Entre os dados armazenados estão:

* Usuários;
* Funcionários;
* EPIs;
* Alertas;
* Ocorrências;
* Registros de conformidade;
* Histórico das análises;
* Dados de autenticação.

A solução pode utilizar PostgreSQL como sistema gerenciador de banco de dados.

---

# 6. Fluxo Principal da Aplicação

O funcionamento simplificado ocorre da seguinte forma:

1. A câmera captura a imagem do trabalhador.

2. A aplicação do operador recebe o frame.

3. O modelo de Inteligência Artificial analisa a imagem.

4. O sistema identifica o trabalhador e os EPIs detectados.

5. As regras de segurança verificam se os EPIs obrigatórios estão presentes.

6. Caso uma irregularidade seja identificada, uma ocorrência é criada.

7. A ocorrência é enviada para a API.

8. A API registra as informações no banco de dados.

9. O painel administrativo recebe o alerta.

10. O responsável pode consultar e acompanhar a ocorrência.

---

# 7. Representação Simplificada

```text
                    ┌───────────────────────┐
                    │       CÂMERA          │
                    └───────────┬───────────┘
                                │
                                ▼
                    ┌───────────────────────┐
                    │ Aplicação Operador    │
                    │                       │
                    │ PySide6               │
                    │ OpenCV                │
                    └───────────┬───────────┘
                                │
                                ▼
                    ┌───────────────────────┐
                    │ Inteligência          │
                    │ Artificial            │
                    │                       │
                    │ YOLO / Visão          │
                    │ Computacional         │
                    └───────────┬───────────┘
                                │
                                ▼
                    ┌───────────────────────┐
                    │        API            │
                    │      FastAPI          │
                    └───────────┬───────────┘
                                │
                   ┌────────────┴────────────┐
                   │                         │
                   ▼                         ▼
        ┌────────────────────┐    ┌────────────────────┐
        │ PostgreSQL         │    │ Aplicação Admin    │
        │                    │    │                    │
        │ Usuários           │    │ Dashboard          │
        │ Alertas            │    │ Alertas            │
        │ Ocorrências        │    │ Relatórios         │
        │ Histórico          │    │ Gestão             │
        └────────────────────┘    └────────────────────┘
```

---

# 8. Evolução da Arquitetura na Sprint 3

Nas Sprints anteriores, a arquitetura estava concentrada principalmente na definição conceitual da solução e nas tecnologias necessárias para utilizar Inteligência Artificial e Visão Computacional.

Durante a Sprint 3, a arquitetura foi refinada para separar melhor as responsabilidades da aplicação.

Foram definidos de forma mais clara:

* Aplicação do operador;
* Aplicação administrativa;
* Camada de Inteligência Artificial;
* API;
* Banco de dados;
* Fluxo de geração e armazenamento dos alertas.

Essa divisão facilita a manutenção, evolução e escalabilidade da aplicação.

Também permite que cada módulo possa evoluir de maneira independente sem comprometer todo o sistema.

---

# 9. Benefícios da Arquitetura

A arquitetura proposta proporciona:

* Separação clara de responsabilidades;
* Maior facilidade de manutenção;
* Melhor organização do código;
* Possibilidade de evolução independente dos módulos;
* Centralização das regras e dados através da API;
* Maior escalabilidade da solução;
* Possibilidade de integração futura com novos dispositivos e sensores.
