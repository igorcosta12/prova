# Casos de Teste e Borda — Zona Azul Digital

Casos derivados de `spec.md`. Notação: **N** = tolerância em minutos; **TETO** = `TETO_DIARIO_CENTAVOS`. Valores concretos seguem as constantes do contrato.

## UC1 — Abrir bilhete

| # | Cenário | Entrada | Esperado |
| --- | --- | --- | --- |
| T1 | Abertura válida sem `entrada` | placa `ABC1D23` | `201`, `status: "aberto"`, `entrada` atual com fuso `-03:00` |
| T2 | Abertura válida com `entrada` | placa válida + `entrada` ISO válida | `201` com a `entrada` informada |
| T3 | Placa com 6 caracteres | `ABC1D2` | `422` `placa_invalida` |
| T4 | Placa minúscula | `abc1d23` | `422` `placa_invalida` |
| T5 | Placa com caractere especial | `ABC-123` | `422` `placa_invalida` |
| T6 | `entrada` malformada | `entrada: "ontem"` | `422` `entrada_invalida` |
| T7 | Placa já com bilhete aberto | 2ª abertura da mesma placa | `409` `bilhete_em_aberto` |
| T8 | Precedência 422 sobre 409 | placa com bilhete aberto + `entrada` malformada | `422` `entrada_invalida` |
| T9 | Reabertura após encerrar | abrir → encerrar → abrir | `201` |
| T10 | Reabertura após cancelar | abrir → cancelar → abrir | `201` |

## UC2 + UC7 — Encerramento e cálculo

| # | Cenário | Duração | Esperado |
| --- | --- | --- | --- |
| T11 | Abaixo da tolerância | N − 1 min | `200`, `valor_centavos: 0` |
| T12 | Exatamente na tolerância | N min 00 s | `valor_centavos: 0` |
| T13 | Tolerância + 1 segundo | N min 01 s | `minutos: N + 1`, cobrança desde o minuto zero |
| T14 | Fração exata | 12 min 00 s | `minutos: 12` |
| T15 | Fração ultrapassada | 12 min 01 s | `minutos: 13` |
| T16 | Abaixo do teto | duração cujo valor < TETO | valor calculado normalmente |
| T17 | Exatamente no teto | duração cujo valor = TETO | `valor_centavos` = TETO |
| T18 | Acima do teto | duração cujo valor > TETO | `valor_centavos` = TETO |
| T19 | Status após encerrar | — | `status: "encerrado"` |
| T20 | Bilhete inexistente | ID desconhecido | `404` `bilhete_nao_encontrado` |
| T21 | Encerrar duas vezes | 2º encerramento | `409` `bilhete_ja_encerrado` |

## UC3 — Ativos

| # | Cenário | Esperado |
| --- | --- | --- |
| T22 | Nenhum bilhete aberto | `200`, lista vazia |
| T23 | Vários abertos | ordenados por `entrada`, mais recente primeiro |
| T24 | Encerrados e cancelados | não aparecem na lista |

## UC4 — Relatório diário

| # | Cenário | Entrada | Esperado |
| --- | --- | --- | --- |
| T25 | Média com 0,5 (parte inteira par) | média = 44,5 min | `45` (o `round()` nativo do Python falharia aqui) |
| T26 | Média sem fração de 0,5 | média = 44,4 min | `44` |
| T27 | Só conta encerrados no dia | encerrados hoje + cancelados + abertos | métricas só dos encerrados hoje |
| T28 | Encerrado em outro dia | bilhete encerrado ontem | fora do relatório de hoje |
| T29 | Dia sem bilhetes | data sem encerramentos | `200` com zerados do contrato |
| T30 | Data malformada | `data=07-10-2026` | `422` `data_invalida` |
| T31 | Data inexistente | `data=2026-02-30` | `422` `data_invalida` |
| T32 | Data ausente | sem parâmetro | `422` `data_invalida` |

## UC5 — Cancelamento

| # | Cenário | Esperado |
| --- | --- | --- |
| T33 | Cancelar bilhete aberto | `200`, `status: "cancelado"`, sem cobrança |
| T34 | Cancelar bilhete encerrado | `409` `bilhete_nao_aberto` |
| T35 | Cancelar duas vezes | `409` `bilhete_nao_aberto` |
| T36 | ID inexistente | `404` `bilhete_nao_encontrado` |

## UC6 — Histórico por placa

| # | Cenário | Esperado |
| --- | --- | --- |
| T37 | Placa sem histórico | `200`, lista vazia |
| T38 | Placa com bilhetes nos três status | todos presentes, mais recente primeiro |
| T39 | Placa inválida na query | `422` `placa_invalida` |

## Transversal

| # | Cenário | Esperado |
| --- | --- | --- |
| T40 | Formato do corpo de erro 422 | idêntico ao definido no contrato |
| T41 | Tipos dos valores monetários | `valor_centavos` sempre inteiro |