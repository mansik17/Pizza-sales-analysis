Here is a `README.md` file for your GitHub repository. It includes descriptions and SQL queries that will provide insight into the database operations for "PizzaHut."

```markdown
# PizzaHut SQL Database Operations

This repository contains SQL queries for analyzing and retrieving insights from a "PizzaHut" database. Below are the operations performed to create and query data in the database related to orders, pizza sales, and revenue analysis.

## Database Setup

1. **Create Database** 
   Create the `PizzaHut` database:
   ```sql
   CREATE DATABASE PIZZAHUT;
   ```

2. **Create Orders Table**  
   Create the `ORDERS` table to store order information:
   ```sql
   CREATE TABLE ORDERS (
       ORDER_ID INT NOT NULL,
       DATE DATE NOT NULL,
       ORDER_TIME TIME NOT NULL,
       PRIMARY KEY (ORDER_ID)
   );
   ```

3. **Create Order Details Table**  
   Create the `ORDER_DETAILS` table to store order-specific pizza details:
   ```sql
   CREATE TABLE ORDER_DETAILS (
       ORDER_DETAILS_ID INT NOT NULL,
       ORDER_ID INT NOT NULL,
       PIZZA_ID TEXT NOT NULL,
       QUANTITY INT NOT NULL,
       PRIMARY KEY (ORDER_DETAILS_ID)
   );
   ```

## Queries and Analysis

1. **Retrieve All Orders**  
   Fetch all records from the `ORDERS` table:
   ```sql
   SELECT * FROM pizzahut.ORDERS;
   ```

2. **Retrieve All Order Details**  
   Fetch all records from the `ORDER_DETAILS` table:
   ```sql
   SELECT * FROM pizzahut.order_details;
   ```

### Business Insights

1. **Retrieve Total Number of Orders Placed**
   ```sql
   SELECT COUNT(ORDER_ID) FROM ORDERS;
   ```

2. **Calculate Total Revenue Generated from Pizza Sales**
   ```sql
   SELECT ROUND(SUM(order_details.QUANTITY * pizzas.PRICE), 2) AS TOTAL_SALES 
   FROM order_details 
   JOIN pizzas ON pizzas.pizza_id = order_details.PIZZA_ID;
   ```

3. **Identify the Highest Priced Pizza**
   ```sql
   SELECT pizza_types.NAME, PIZZAS.PRICE 
   FROM PIZZA_TYPES 
   JOIN PIZZAS ON pizza_types.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID 
   ORDER BY PIZZAS.PRICE DESC 
   LIMIT 1;
   ```

4. **Identify the Most Common Pizza Size Ordered**
   ```sql
   SELECT pizzas.SIZE, COUNT(ORDER_DETAILS.ORDER_DETAILS_ID) AS ORDER_COUNT 
   FROM PIZZAS 
   JOIN ORDER_DETAILS ON pizzas.pizza_id = order_details.PIZZA_ID 
   GROUP BY pizzas.SIZE 
   ORDER BY ORDER_COUNT DESC;
   ```

5. **List the Top 5 Most Ordered Pizza Types Along with Their Quantity**
   ```sql
   SELECT pizza_types.NAME, SUM(order_details.QUANTITY) AS QUANTITY 
   FROM PIZZA_TYPES 
   JOIN PIZZAS ON pizza_types.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID 
   JOIN order_details ON order_details.PIZZA_ID = pizzas.pizza_id 
   GROUP BY pizza_types.NAME 
   ORDER BY QUANTITY DESC 
   LIMIT 5;
   ```

6. **Join Tables to Find Total Quantity of Each Pizza Category**
   ```sql
   SELECT pizza_types.CATEGORY, SUM(order_details.QUANTITY) AS QUANTITY 
   FROM pizza_types 
   JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id 
   JOIN order_details ON order_details.PIZZA_ID = pizzas.pizza_id 
   GROUP BY pizza_types.CATEGORY 
   ORDER BY QUANTITY DESC;
   ```

7. **Determine Distribution of Orders by Hour of the Day**
   ```sql
   SELECT HOUR(ORDER_TIME) AS HOUR, COUNT(ORDER_ID) AS ORDER_COUNT 
   FROM ORDERS 
   GROUP BY HOUR(ORDER_TIME);
   ```

8. **Find Category-wise Distribution of Pizzas**
   ```sql
   SELECT pizza_types.CATEGORY, COUNT(NAME) 
   FROM pizza_types 
   GROUP BY CATEGORY;
   ```

9. **Group Orders by Date and Find Average Number of Pizzas Ordered per Day**
   ```sql
   SELECT ROUND(AVG(QUANTITY), 0) AS avg_pizza_ordered_per_day 
   FROM (SELECT orders.order_time, SUM(order_details.QUANTITY) AS QUANTITY 
         FROM ORDERS 
         JOIN order_details ON ORDERS.ORDER_ID = order_details.ORDER_ID 
         GROUP BY orders.order_time) AS ORDER_QUANTITY;
   ```

10. **Top Three Most Ordered Pizzas Based on Revenue**
    ```sql
    SELECT pizza_types.name, SUM(order_details.quantity * pizzas.price) AS revenue 
    FROM pizza_types 
    JOIN pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id 
    JOIN order_details ON order_details.PIZZA_ID = pizzas.pizza_id 
    GROUP BY pizza_types.name 
    ORDER BY revenue DESC 
    LIMIT 3;
    ```

11. **Calculate Percentage Contribution of Each Pizza Type to Total Revenue**
    ```sql
    SELECT pizza_types.category, 
           ROUND(SUM(order_details.QUANTITY * pizzas.price) / 
                 (SELECT ROUND(SUM(order_details.QUANTITY * pizzas.PRICE), 2) 
                  AS Total_sales 
                  FROM order_details 
                  JOIN pizzas ON pizzas.pizza_id = order_details.PIZZA_ID) * 100, 2) AS revenue
    FROM pizza_types 
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id 
    JOIN order_details ON order_details.PIZZA_ID = pizzas.pizza_id 
    GROUP BY pizza_types.category 
    ORDER BY revenue DESC;
    ```

12. **Analyze the Cumulative Revenue Generated Over Time**
    ```sql
    SELECT order_date, SUM(revenue) OVER (ORDER BY order_date) AS cum_revenue 
    FROM (SELECT orders.ORDER_date, SUM(order_details.QUANTITY * pizzas.price) AS revenue 
          FROM order_details 
          JOIN pizzas ON order_details.PIZZA_ID = pizzas.pizza_id 
          JOIN orders ON orders.ORDER_ID = order_details.order_id 
          GROUP BY orders.order_date) AS sales;
    ```

13. **Determine the Top 3 Most Preferred Pizza Types Based on Revenue for Each Pizza Category**
    ```sql
    SELECT name, revenue 
    FROM (SELECT category, name, revenue, 
                 RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn 
          FROM (SELECT pizza_types.category, pizza_types.name, 
                       SUM((order_details.quantity) * pizzas.price) AS revenue 
                FROM pizza_types 
                JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id 
                JOIN order_details ON order_details.pizza_id = pizzas.pizza_id 
                GROUP BY pizza_types.category, pizza_types.name) AS A) AS B 
    WHERE rn <= 3;
    ```




```

You can copy this into your GitHub repository `README.md` file to provide a detailed description of your PizzaHut database and its SQL operations.
