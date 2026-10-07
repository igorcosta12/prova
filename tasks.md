# Tarefas de Implementação — Zona Azul Digital

Cada tarefa referencia os UCs de `spec.md` e os testes de `tests.md`.

| # | Tarefa | UCs | Testes | Pronto quando |
| --- | --- | --- | --- | --- |
| 1 | Setup do projeto (FastAPI, estrutura base, dependências) | — | — | aplicação sobe sem erros |
| 2 | Schemas Pydantic: validação estrita de placas (7 alfanuméricos maiúsculos), `entrada` ISO 8601 com fuso `-03:00`, `data` AAAA-MM-DD | UC1, UC4, UC6 | T3–T6, T30–T32 | entradas inválidas são rejeitadas |
| 3 | *Exception handler* customizado para erros 422 no formato exato do contrato | todos | T8, T40 | corpo de erro idêntico ao contrato |
| 4 | Armazenamento em memória: índice por `id` e índice de ativos por `placa` | UC8 | — | estruturas criadas e testáveis |
| 5 | `POST /bilhetes` com regra de vaga única e `entrada` opcional | UC1, UC8 | T1–T10 | testes de UC1 passando |
| 6 | Motor de cálculo: minutos com arredondamento para cima (*ceil*), tolerância, tarifa e teto diário em centavos inteiros | UC2, UC7 | T11–T18, T41 | testes de cálculo passando |
| 7 | `POST /bilhetes/{id}/encerramento`: transição para `"encerrado"` e liberação da placa | UC2 | T19–T21 | testes de UC2 passando |
| 8 | `POST /bilhetes/{id}/cancelamento`: transição para `"cancelado"` e liberação da placa | UC5 | T33–T36 | testes de UC5 passando |
| 9 | `GET /bilhetes/ativos` com ordenação por entrada decrescente | UC3 | T22–T24 | testes de UC3 passando |
| 10 | `GET /bilhetes?placa=` com histórico completo e ordenação | UC6 | T37–T39 | testes de UC6 passando |
| 11 | `GET /relatorios/diario` com cálculo de média arredondada para cima (evitando arredondamento par nativo do Python) | UC4 | T25–T29 | testes de UC4 passando |
| 12 | Validação local com a suíte e execução do script de auto-correção | todos | todos | CI sem falhas nos testes públicos |
| 13 | Entrega: atualizar `ALUNO.md`, preencher `FONTES.md` registrando a colaboração técnica, push final e fechar a issue | — | — | `teacher.json` gerado com sucesso |