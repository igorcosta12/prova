# Especificação de Requisitos (Spec) — Zona Azul Digital

Requisitos funcionais derivados do contrato oficial. Valores monetários em centavos inteiros; erros de formato (422) precedem conflitos de estado (409), conforme `constitution.md`.

## Ciclo de Vida do Bilhete
* **`aberto`**: Criado via abertura (UC1). Pode ser encerrado (UC2) ou cancelado (UC5).
* **`encerrado`**: Estado final após o encerramento bem-sucedido (UC2).
* **`cancelado`**: Estado final após o cancelamento (UC5).

As listagens e históricos são ordenados estritamente pela data de `entrada`, da mais recente para a mais antiga.

## UC1 — Abrir bilhete (`POST /bilhetes`)
* **Critério 1**: Enviar corpo com `placa` (7 caracteres alfanuméricos maiúsculos) gera resposta `201 Created` contendo `id`, `placa`, `entrada` (ISO-8601 com fuso `-03:00`) e `status: "aberto"`.
* **Critério 2**: O campo `entrada` é opcional. 
* **Erros**: Placa ausente/inválida gera `422` (`placa_invalida`). Entrada malformada gera `422` (`entrada_invalida`). Placa com bilhete ativo gera `409` (`bilhete_em_aberto`).

## UC2 — Encerrar bilhete (`POST /bilhetes/{id}/encerramento`)
* **Critério 1**: Retorna `200 OK` informando dados do bilhete, `status: "encerrado"`, `minutos` e `valor_centavos` inteiro.
* **Critério 2**: O cálculo converte o tempo em minutos (arredondando frações para cima), aplica a tolerância gratuita (UC7), calcula a tarifa horária proporcional e limita ao teto diário.
* **Erros**: Bilhete inexistente gera `404` (`bilhete_nao_encontrado`). Bilhete já encerrado gera `409` (`bilhete_ja_encerrado`).

## UC3 — Listar ativos (`GET /bilhetes/ativos`)
* **Critério 1**: Retorna `200 OK` com array dos bilhetes abertos, ordenados do mais recente para o mais antigo.

## UC4 — Relatório diário (`GET /relatorios/diario?data=AAAA-MM-DD`)
* **Critério 1**: Retorna `200 OK` com total de bilhetes, faturamento em centavos e tempo médio (considerando apenas bilhetes encerrados no dia, arredondando 0,5 para cima).
* **Erros**: Data ausente ou fora do formato `AAAA-MM-DD` gera `422` (`data_invalida`).

## UC5 — Cancelar bilhete (`POST /bilhetes/{id}/cancelamento`)
* **Critério 1**: Altera o estado de bilhetes abertos para `"cancelado"` (`200 OK`), sem gerar saída ou cobrança.
* **Erros**: Bilhete inexistente gera `404` (`bilhete_nao_encontrado`). Bilhete não aberto (já encerrado ou cancelado) gera `409` (`bilhete_nao_aberto`).

## UC6 — Histórico por placa (`GET /bilhetes?placa=ABC1D23`)
* **Critério 1**: Retorna `200 OK` com todo o histórico de bilhetes da placa informada (qualquer status), mais recentes primeiro. Placa sem histórico retorna array vazio.
* **Erros**: Placa ausente ou inválida gera `422` (`placa_invalida`).

## UC7 — Tolerância gratuita
* **Critério 1**: Duração menor ou igual à tolerância resulta em `valor_centavos: 0`. Caso contrário, cobra integralmente desde o primeiro minuto, sem descontar a tolerância, respeitando o teto diário.

## UC8 — Uma vaga por placa
* **Critério 1**: Tentar abrir bilhete para uma placa com bilhete ativo gera `409` (`bilhete_em_aberto`). As validações de formato (422) têm precedência absoluta sobre esta regra.