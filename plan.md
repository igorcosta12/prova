# Plano Técnico — Zona Azul Digital

## Stack
- **Python + FastAPI + Pydantic**: validação de esquemas e suporte nativo a datas ISO 8601 com fuso horário (`-03:00`).

## Persistência
- **Em memória**: bilhetes ativos mantidos em um dicionário indexado pela placa. Isso garante a regra de vaga única: uma placa não pode ter dois bilhetes abertos simultaneamente. Uma nova abertura para a mesma placa resulta em **409** (`bilhete_em_aberto`).

## Validação e Erros
- Validação de formato feita pelo Pydantic **antes** de qualquer consulta ao estado.
- Uso de um *exception handler* customizado para garantir que os erros de validação **422** retornem exatamente o formato de corpo exigido pelo contrato, sobrepondo o comportamento padrão do FastAPI.

## Tempo
- O campo opcional `entrada` (ISO-8601 com fuso `-03:00`) permite informar o horário de início customizado. Quando ausente, assume-se o relógio atual. Isso serve como gancho de testabilidade para simular frações e tetos sem esperar tempo real.

## Cálculo da Tarifa
- Todos os cálculos são feitos estritamente em **centavos inteiros** (sem ponto flutuante).
- O tempo é cobrado por frações de `FRACAO_MINUTOS` minutos, arredondando **sempre para cima** (fração exata cobra 1 fração; 1 minuto a mais cobra a fração seguinte).

> [!WARNING]
> **Teto Diário**: o `valor_centavos` de um bilhete nunca supera o limite de `TETO_DIARIO_CENTAVOS`, independentemente de quantas horas o veículo permaneça estacionado.