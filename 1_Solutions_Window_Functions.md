## Day 1 - Window Functions Solutions

1.  
SELECT customer_id, order_id, amount,  
  SUM(amount) OVER (  
    PARTITION BY customer_id  
    ORDER BY order_date  
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW  
  ) AS running_total  
FROM orders;

2.  
SELECT customer_id, order_id, amount,  
  DENSE_RANK() OVER (  
    PARTITION BY customer_id  
    ORDER BY amount DESC  
  ) AS spend_rank  
FROM orders;  

3.  
SELECT customer_id, order_id, order_date, amount,  
  LAG(amount, 1) OVER (  
    PARTITION BY customer_id  
    ORDER BY order_date  
  ) AS prev_amount  
FROM orders;

4.  
SELECT customer_id, order_id, amount,  
  AVG(amount) OVER (PARTITION BY customer_id) AS customer_avg_spend  
FROM orders;

5.  
SELECT customer_id, order_id, amount  
FROM (  
  SELECT customer_id, order_id, amount,  
    RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC) AS rnk  
  FROM orders  
) ranked  
WHERE rnk = 1;  


