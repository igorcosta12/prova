# Constituição do Projeto — Zona Azul Digital

### 1. Valores monetários sempre em centavos inteiros
Todo valor financeiro (tarifas, totais, cálculos intermediários) é representado, processado e armazenado como **número inteiro em centavos** (ex.: `valor_centavos`). Valores decimais ou de ponto flutuante não são permitidos.
* **Motivo:** Evitar erros de arredondamento de ponto flutuante e garantir resultados exatos e reproduzíveis.

### 2. Precedência de Erros e Validação
A validação de formato e esquema da requisição (erros HTTP 422, como placa inválida ou data malformada) tem precedência absoluta sobre as regras de negócio de estado (erros HTTP 409). Requisições malformadas nunca geram conflitos de estado.

### 3. Fidelidade Estricta ao Contrato
Os caminhos de rotas, os códigos de status HTTP, os nomes exatos das chaves JSON e os tipos de dados definidos no contrato oficial e no servidor de homologação não podem ser modificados sob nenhuma hipótese.