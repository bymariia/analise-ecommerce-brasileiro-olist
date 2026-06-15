# Relatório de Qualidade dos Dados

## Resumo
| Métrica | Antes | Depois |
|---------|-------|--------|
| Linhas na tabela orders | 99.441 | 99.441 |
| % nulos em order_delivered_customer_date | 2.98% | 2.98% (mantidos como NaT) |
| Linhas duplicadas após JOINs | N/A | 0 |
| Produtos sem categoria | 610 | 0 |

## Decisões de Limpeza e Transformação

1. **Nulos em `order_delivered_customer_date`:** - **Decisão:** Mantidos como nulos (`NaT`). 
   - **Justificativa:** Após análise, verificou-se que a grande maioria estava com status `shipped` ou `canceled`. O nulo é informativo (o pedido de fato não foi entregue). Foi criada a flag booleana `foi_entregue` para facilitar os filtros futuros no Power BI.

2. **Nulos nas dimensões de produtos (peso, comprimento, altura, largura):**
   - **Decisão:** Preenchidos utilizando a mediana agrupada pela categoria do produto.
   - **Justificativa:** A mediana é mais robusta a *outliers* do que a média. Fazer o preenchimento isolado por categoria garante que não misturemos o peso de uma "geladeira" com o de um "brinco".

3. **Armadilha de produtos sem categoria (610 itens):**
   - **Decisão:** Preenchidos com a string `'unknown'` antes da operação de agrupamento.
   - **Justificativa:** O método `groupby` do Pandas ignora chaves nulas por padrão, o que estava corrompendo os dados e gerando novos valores nulos nas dimensões físicas. 

4. **Duplicatas potenciais nas avaliações (`reviews`):**
   - **Decisão:** Aplicado o método `drop_duplicates` mantendo apenas a avaliação mais recente (`keep='last'`) por pedido.
   - **Justificativa:** Evitar a multiplicação de linhas (relação 1:N) durante a construção da *Master Table*, visto que alguns clientes atualizaram suas notas de avaliação.

5. **Agregação de itens (`order_items`) e pagamentos (`payments`):**
   - **Decisão:** Agrupados por `order_id` com sumarização (soma de valores, contagem de itens) antes do *merge* principal.
   - **Justificativa:** Garantir a integridade da *Master Table* para que ela mantenha a granularidade exata de **1 linha = 1 pedido**.