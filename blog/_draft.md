```mermaid

scatter
  title Clean Architecture: Abstraktion vs Instabilität
  xAxis "Instabilität (I)" 0 --> 1
  yAxis "Abstraktion (A)" 0 --> 1
  "com.shop.order" : 0.25, 0.67
  "com.shop/customer" : 0.33, 0.67
  "com.shop.order.usecase" : 0.40, 0.40
  "com.shop.customer.usecase" : 0.50, 0.40
  "com.shop.order.rest" : 0.60, 0.20
  "com.shop.customer.ui" : 0.60, 0.00
  "com.shop.persistence" : 0.50, 0.20
  "jakarta.json" : 1.00, 0.00
  "spring.data" : 1.00, 0.00

```
