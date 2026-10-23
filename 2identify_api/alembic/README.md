# Estratégia de baseline do Alembic

O banco existente foi criado pelo projeto Admin e já possui uma tabela
`alembic_version`. Para manter uma única linha histórica, as quatro revisões do Admin são
copiadas sem alterações para esta pasta.

Na etapa de ingestão e tratamento administrativo de alertas:

- a revisão manual `7b9d2e4f6a81` cria somente `alertas_ingestao`;
- a revisão `c8f1e2a3b4d5` adiciona a auditoria de confirmação e o índice do histórico;
- a revisão `e4a7b8c9d0e1` cria `areas_risco`, `operacoes` e `operacao_epis`;
- a revisão preserva as tabelas de ocorrências e alertas existentes;
- o `autogenerate` permanece bloqueado, pois a API ainda não possui metadados completos;
- a mesma revisão existe no Admin e na API para manter uma única linhagem.

> Nota da cópia acadêmica (Sprint 4): as revisões `f1b2c3d4e5f6` e `a3c5e7f9b1d2` existem somente
> nesta pasta da API; aplique as migrações a partir da API.

Depois de backup e conferência da conexão, aplique uma única vez, a partir da API:

```powershell
python -m alembic upgrade head
```

Não execute o upgrade nas duas pastas: ambas usam a mesma tabela `alembic_version` e o mesmo banco.
