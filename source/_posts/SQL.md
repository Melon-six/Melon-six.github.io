---
Tetley：SOL
date：2025-3-3
---



```
# SQL 系统学习笔记

## 一、SQL基础架构
```sql
SELECT [DISTINCT] <字段列表>
FROM <表名>
[WHERE <条件>]
[GROUP BY <分组字段>]
[HAVING <分组条件>]
[ORDER BY <排序字段>]
[LIMIT <数量>];
```

------

## 二、核心操作分类

### 1. DDL（数据定义语言）

| 命令     | 功能           | 示例                              |
| :------- | :------------- | :-------------------------------- |
| CREATE   | 创建数据库/表  | `CREATE TABLE users (...)`        |
| ALTER    | 修改表结构     | `ALTER TABLE users ADD COLUMN...` |
| DROP     | 删除数据库/表  | `DROP TABLE temp_data`            |
| TRUNCATE | 快速清空表数据 | `TRUNCATE TABLE logs`             |

**表创建示例**：

```
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    salary DECIMAL(10,2),
    hire_date DATE DEFAULT CURRENT_DATE,
    INDEX idx_name (name)
);
```

------

### 2. DML（数据操作语言）

| 命令   | 功能     | 示例                             |
| :----- | :------- | :------------------------------- |
| INSERT | 插入数据 | `INSERT INTO users VALUES (...)` |
| UPDATE | 更新记录 | `UPDATE products SET price=...`  |
| DELETE | 删除记录 | `DELETE FROM orders WHERE...`    |

**批量插入示例**：

```
INSERT INTO departments (dept_name, budget)
VALUES 
    ('Sales', 500000),
    ('IT', 750000),
    ('HR', 300000);
```

------

### 3. DQL（数据查询语言）

#### 基础查询

```
SELECT product_id AS ID, 
       product_name "Product Name",
       unit_price * 0.9 AS discount_price
FROM products
WHERE category_id = 3
   AND stock_quantity > 100;
```

#### 高级查询

```
-- JOIN示例
SELECT o.order_id, c.customer_name, SUM(oi.quantity * oi.unit_price) total
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.order_date BETWEEN '2023-01-01' AND '2023-12-31'
GROUP BY o.order_id, c.customer_name
HAVING total > 1000
ORDER BY total DESC
LIMIT 10;
```

------

## 三、关键函数速查

### 1. 聚合函数

| 函数      | 说明        | 示例                   |
| :-------- | :---------- | :--------------------- |
| COUNT()   | 统计行数    | `COUNT(DISTINCT dept)` |
| SUM()     | 求和        | `SUM(sales_amount)`    |
| AVG()     | 平均值      | `AVG(rating)`          |
| MAX/MIN() | 最大/最小值 | `MAX(temperature)`     |

### 2. 字符串函数

```
CONCAT(first_name, ' ', last_name) AS full_name,
SUBSTRING(phone, 1, 3) AS area_code,
LENGTH(address) AS addr_length,
REPLACE(description, 'old', 'new')
```

### 3. 日期函数

```
CURRENT_DATE() AS today,
DATE_ADD(hire_date, INTERVAL 1 YEAR) AS anniversary,
DATEDIFF(NOW(), order_date) AS days_passed
```

------

## 四、高级功能

### 1. 事务控制

```
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;
COMMIT;  -- 或 ROLLBACK
```

### 2. 窗口函数

```
SELECT 
    employee_id,
    department_id,
    salary,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS dept_rank,
    AVG(salary) OVER (PARTITION BY department_id) AS dept_avg_salary
FROM employees;
```

### 3. CTE（公共表表达式）

```
WITH regional_sales AS (
    SELECT region, SUM(amount) total_sales
    FROM orders
    GROUP BY region
)
SELECT region, total_sales
FROM regional_sales
WHERE total_sales > 1000000;
```

------

## 五、性能优化要点

### 1. 索引策略

- 为WHERE/JOIN/ORDER BY字段创建索引
- 避免过度索引（写操作性能影响）
- 使用复合索引时注意字段顺序

### 2. 查询优化

- 避免使用`SELECT *`
- 慎用`LIKE '%前缀%'`
- 合理使用EXPLAIN分析执行计划
- 注意隐式类型转换

### 3. 设计规范

- 使用合适的字段类型（如INT vs VARCHAR）
- 遵循数据库范式（但不要过度设计）
- 建立有效的外键约束
- 定期进行表维护（OPTIMIZE TABLE）

------

## 六、安全最佳实践

1. 使用预处理语句防止SQL注入

   ```
   # Python示例
   cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
   ```

2. 遵循最小权限原则

3. 定期备份重要数据

4. 敏感数据加密存储

5. 审计日志记录

------

## 七、不同数据库差异速查

| 特性       | MySQL          | PostgreSQL | SQLite        |          |      |      |      |
| :--------- | :------------- | :--------- | :------------ | -------- | ---- | ---- | ---- |
| 自增字段   | AUTO_INCREMENT | SERIAL     | AUTOINCREMENT |          |      |      |      |
| 字符串拼接 | CONCAT()       | `          |               | ` 操作符 | `    |      | `    |
| 时间类型   | DATETIME       | TIMESTAMP  | TEXT          |          |      |      |      |
| 正则表达式 | REGEXP         | ~ 操作符   | 需扩展        |          |      |      |      |