# Constituição do Projeto — Zona Azul Digital

## Regras Operacionais e Convenções Imutáveis

1. **Valores Monetários Inteiros**: Todos os montantes financeiros (tarifas, tetos e cálculos) devem ser processados e armazenados como números inteiros em centavos, eliminando erros de arredondamento de ponto flutuante (ex.: `valor_centavos` em vez de decimais).
2. **Precedência de Erros Rigorosa**: A validação de formato da requisição (erros HTTP 422, como placa inválida ou entrada malformada) tem precedência absoluta sobre as regras de negócio de conflito de estado (erros HTTP 409).
3. **Respeito Absoluto ao Contrato**: Os caminhos de rotas, os códigos de estado HTTP, os nomes exatos das chaves JSON e os tipos de dados definidos no contrato oficial não podem ser modificados sob nenhuma hipótese.