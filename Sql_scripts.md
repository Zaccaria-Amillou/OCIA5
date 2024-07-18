# Requetes SQL

- [x] En excluant les commandes annulées, quelles sont les commandes récentes de moins de 3 mois que les clients ont reçues avec au moins 3 jours de retard ? 

```sql
SELECT
    o.order_id as 'id ordre',
    (julianday(order_estimated_delivery_date) - julianday(order_delivered_customer_date)) as 'j_diff'
FROM
    orders o
WHERE
    (julianday(order_estimated_delivery_date) - julianday(order_delivered_customer_date)) >= 3
AND
    STRFTIME('%m', order_approved_at) >= 3
AND
    order_status NOT LIKE 'canceled'

```


- [x] Qui sont les vendeurs ayant généré un chiffre d'affaires de plus de 100 000 Real sur des commandes livrées via Olist ? 

```sql
SELECT
    oi.seller_id,
    COUNT(oi.product_id),
    SUM(op.payment_installments * op.payment_value) AS total
FROM order_items oi
LEFT JOIN orders o ON oi.order_id = o.order_id
LEFT JOIN order_pymts op ON o.order_id = op.order_id
GROUP BY oi.seller_id
HAVING SUM(op.payment_installments * op.payment_value) > 100000;

```
 
- [x] Qui sont les nouveaux vendeurs (moins de 3 mois d'ancienneté) qui sont déjà très engagés avec la plateforme (ayant déjà vendu plus de 30 produits) ?  
```sql
SELECT
    seller_id,
    COUNT(oi.product_id) AS 'nombre produits',
    order_purchase_timestamp
FROM
    order_items oi
JOIN orders o ON o.order_id = oi.order_id
WHERE strftime('%Y-%m-%d %H:%M:%S', o.order_purchase_timestamp) < strftime('%Y-%m-%d %H:%M:%S', '2018-10-17 17:30:18', '-3 months')
GROUP BY
    seller_id
HAVING
    COUNT(oi.product_id) >= 30
ORDER BY "nombre product" DESC;

```

- [x] Quels sont les 5 codes postaux, enregistrant plus de 30 commandes, avec le pire review score moyen sur les 12 derniers mois ? 

```sql
SELECT
    c.customer_zip_code_prefix as ZIP,
    AVG(review_score)
FROM order_reviews or2
JOIN orders o ON or2.order_id = o.order_id
JOIN main.customers c on o.customer_id = c.customer_id
GROUP BY c.customer_zip_code_prefix
ORDER BY AVG(review_score) ASC;

```