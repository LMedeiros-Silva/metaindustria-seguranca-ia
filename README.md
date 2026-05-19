# SafeVision Metaindústria

## Integrantes

- Leonardo Medeiros — RM: 559220
- Arthur Bergamaço Alves — RM: 556207
- Breno Barbosa Pereira — RM: 555348
- Mateus de Souza Santos — RM: 559118
- Pietro Rodriguez — RM: 555899
- Renan Melo Rosembeng — RM: 558535 

## Problema Abordado

Ambientes industriais apresentam riscos relacionados ao uso incorreto de EPIs, posturas inadequadas e aproximação de áreas perigosas. Muitas vezes, a fiscalização depende apenas da supervisão humana, o que pode gerar atrasos na identificação de situações de risco.

## Proposta de Solução

O SafeVision Metaindústria é uma solução de segurança industrial proativa baseada em Inteligência Artificial e Visão Computacional.

O sistema utiliza câmeras para monitorar funcionários em tempo real, detectar EPIs, identificar posturas de risco e verificar entrada em áreas perigosas cadastradas pelo usuário.

Quando um risco é identificado, o sistema gera alertas visuais na aplicação e poderá acionar dispositivos físicos, como Stack Light industrial.

## Tecnologias Selecionadas

- Python
- YOLO
- SAM
- OpenCV
- Banco de dados
- GitHub
- Mermaid/Draw.io para UML

## Justificativa Técnica

Python foi escolhido por possuir grande suporte a bibliotecas de Inteligência Artificial e Visão Computacional.

YOLO foi escolhido por permitir detecção de objetos em tempo real.

SAM foi escolhido para apoiar segmentação corporal e análise de postura.

OpenCV foi escolhido para captura e processamento de imagens.

O banco de dados será utilizado para armazenar usuários, alertas, áreas de risco, registros de conformidade e histórico de ocorrências.

## Documentação

- [Requisitos](docs/requisitos.md)
- [Personas](docs/personas.md)
- [Restrições](docs/restricoes.md)
- [Diagrama de Casos de Uso](diagramas/casos-de-uso.md)
- [Diagrama de Atividades](diagramas/atividades.md)
- [Diagrama de Classes](diagramas/classes.md)
