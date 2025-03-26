1. **Find the total number of transactions per city.**  
   - *Hint: Use `JOIN` with `GROUP BY`.*
```sql
    select COUNT(transaction_id) as "Total Transactions" , city
    from dataanalytics.sales_transactions
    join dataanalytics.customer_info
    on customer_info.customer_id = sales_transactions.customer_id
    group by customer_info.city 
    order by "Total Transactions" desc;
```

2. **Retrieve the top 5 most purchased products based on total quantity sold.**  
   - *Hint: Use `GROUP BY` and `ORDER BY`.*
```sql
    select product_name,sum(quantity) as "Product Quantity"
    from dataanalytics.sales_transactions
    group by product_name,quantity 
    order by "Product Quantity" desc
    limit 5;
```

3. **Find the average transaction amount per category.**  
   - *Hint: Use `AVG()` with `GROUP BY`.*
```sql
select category, avg(total_amount)::numeric(20,2) "Average Total Amount"
from dataanalytics.sales_transactions
group by category
order by 2 desc;
```
4. **Identify the payment method that has the highest total sales.**  
   - *Hint: Use `SUM(total_amount)` with `GROUP BY payment_method`.*
```sql
select payment_method, sum(total_amount) "Total Sales"
from dataanalytics.sales_transactions
group by payment_method
limit 1;
```

5. **Find customers who have made at least 5 transactions.**  
   - *Hint: Use `COUNT(transaction_id) HAVING COUNT(transaction_id) >= 5`.*
```sql
select first_name,last_name, COUNT(transaction_id) "Transaction Count"
from dataanalytics.customer_info
join dataanalytics.sales_transactions
on customer_info.customer_id = sales_transactions.customer_id
group by first_name,last_name
having (COUNT(transaction_id)) >= 5
order by "Transaction Count" desc;
```

6. **Retrieve all customers who registered in the last 6 months but have not made any transactions.**  
   - *Hint: Use `LEFT JOIN` and `IS NULL`.*
```sql
select first_name,last_name, registration_date
from dataanalytics.customer_info
left join dataanalytics.sales_transactions
on customer_info.customer_id = sales_transactions.customer_id
where transaction_id is null
and registration_date >= current_date - interval '6 months';
```

7. **Find the total revenue generated in each year from sales transactions.**  
   - *Hint: Use `DATE_PART('year', transaction_date)` or `EXTRACT(YEAR FROM transaction_date)`.*
```sql
select sum(total_amount) "Total Revenue", extract(year from transaction_date) "Sales Year"
from dataanalytics.sales_transactions
group by transaction_date
order by 1 desc;
```

8. **List the number of unique products sold in each category.**  
   - *Hint: Use `COUNT(DISTINCT product_name)`.*
```sql
select count(distinct product_name) "TOTAL UNIQUE PRODUCTS"
from dataanalytics.sales_transactions;
```
9. **Find all customers who have made purchases across at least 3 different product categories.**  
   - *Hint: Use `COUNT(DISTINCT category) HAVING >= 3`.*
```sql
select first_name,last_name, COUNT(distinct category)"Count Category"
from dataanalytics.customer_info
join dataanalytics.sales_transactions
on customer_info.customer_id = sales_transactions.customer_id
group by first_name,last_name
having (COUNT(distinct category)) >= 3
order by "Count Category" desc;
```

10. **Identify the most popular purchase day of the week based on transaction count.**  
   - *Hint: Use `TO_CHAR(transaction_date, 'Day')` or `EXTRACT(DOW FROM transaction_date)`.*
```sql
select extract(dow from transaction_date), count(transaction_id) "Transaction Count"
from dataanalytics.sales_transactions
group by transaction_date
order by 2 desc;
```

---

##  **Advanced-Level Questions.**

11. **Find the top 3 customers who have spent the most in the last 12 months.**  
    - *Hint: Use `SUM(total_amount)` and filter on `transaction_date`.*
```sql
select first_name,last_name, sum(total_amount) "Amount Spent", transaction_date
from customer_info
join sales_transactions
on customer_info.customer_id = sales_transactions.customer_id
group by first_name,last_name,transaction_date
order by 3 desc;
```

12. **Determine the percentage of total revenue contributed by each product category.**  
    - *Hint: Use `SUM(total_amount) * 100.0 / (SELECT SUM(total_amount) FROM sales_transactions)`.*
```sql
select category, (sum (total_amount) * 100.0/
(select sum(total_amount)from sales_transactions)) ::numeric(20,2) as "Percentage Revenue "
from sales_transactions
group by category
order by 2 desc;
```

** ASSIGNMENT **

13. **Find the month-over-month sales growth for the last 12 months.**  
    - *Hint: Use `LAG(SUM(total_amount)) OVER (ORDER BY transaction_date)` to compare months.*

14. **Identify customers who have increased their spending by at least 30% compared to the previous year.**  
    - *Hint: Use `SUM(total_amount) FILTER (WHERE YEAR = X)` vs `SUM(total_amount) FILTER (WHERE YEAR = X-1)`.*

15. **Find the first purchase date for each customer.**