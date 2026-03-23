# Chapter 1. Window functions -  Theory

A regular GROUP BY collapses rows. You ask "what's the total sales per region?" and you get one row per region — the individual rows are gone.
A window function keeps every row and adds a new column computed across a defined "window" of rows. The window can be the entire table, a partition (like "all rows for this customer"), or a sliding frame (like "the last 3 rows").  
Think of it as: "For each row, look at this set of rows and compute something — but don't collapse anything."  

## The syntax skeleton 
function_name(column)  OVER (  
    PARTITION BY  partition_column   -- optional: defines the "group"  
    ORDER BY      order_column       -- optional: defines row order within partition  
    ROWS/RANGE    frame_clause       -- optional: sliding window  
)

PARTITION BY — like GROUP BY but without collapsing. Resets the window for each group. If you omit it, the entire table is one window.
ORDER BY — required for ranking and offset functions. Defines which row is "first", "second" etc within the partition.
ROWS frame — for running totals and moving averages. ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW means "from the very first row up to this row."

## The 5 function families you must know

1. Ranking — ROW_NUMBER(), RANK(), DENSE_RANK()  
SELECT name, score,
  ROW_NUMBER() OVER (ORDER BY score DESC)  AS row_num,  
  RANK()       OVER (ORDER BY score DESC)  AS rnk,  
  DENSE_RANK() OVER (ORDER BY score DESC)  AS dense_rnk  
FROM students;  

ROW_NUMBER → 1, 2 (arbitrary tiebreak — no true ties)  
RANK → 1, 1, 3 (next rank skips)  
DENSE_RANK → 1, 1, 2 (no gap)  



2. Offset — LAG(), LEAD()  
SELECT date, revenue,  
  LAG(revenue, 1)  OVER (ORDER BY date)  AS prev_day_revenue,  
  LEAD(revenue, 1) OVER (ORDER BY date)  AS next_day_revenue  
FROM daily_sales;  


LAG(col, n) looks n rows behind. LEAD(col, n) looks n rows ahead. Both accept a third argument as a default if the row doesn't exist: LAG(revenue, 1, 0).  


3. Aggregate over window — SUM(), AVG(), COUNT(), MAX(), MIN()  
SELECT customer_id, order_date, amount,  
  SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date  
                      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total,  
  AVG(amount) OVER (PARTITION BY customer_id) AS customer_avg  
FROM orders;  


4. Percentile / distribution — NTILE(), PERCENT_RANK(), CUME_DIST()  
SELECT name, score,  
  NTILE(4) OVER (ORDER BY score DESC)  AS quartile  
FROM students;  


-- quartile 1 = top 25%, quartile 4 = bottom 25%  


5. First/Last value — FIRST_VALUE(), LAST_VALUE()  
sqlSELECT customer_id, order_date, amount,  
  FIRST_VALUE(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS first_order_value  
FROM orders;   


      
### Look out for the exercises on this chapter
