## Tabela: orders

| Coluna | Tipo | Descrição | Observações |
|--------|------|-----------|-------------|
| order_id | string | Identificador único do pedido | Chave primária |
| customer_id | string | ID do cliente (FK para customers) | |
| order_status | string | Status: delivered, shipped, canceled... | 8 valores únicos |
| order_purchase_timestamp | datetime | Momento da compra | Precisa converter |
| order_delivered_customer_date | datetime | Data real de entrega | ~3% nulos (pedidos não entregues) |
| order_estimated_delivery_date | datetime | Data prometida de entrega | Base para cálculo de atraso |