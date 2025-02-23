# PostgreSQL: Загрузка данных и SQL-запросы

## Создание таблиц

```sql
-- Создание таблицы customer
CREATE TABLE customer (
    customer_id SERIAL PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    gender TEXT,
    DOB DATE,
    job_title TEXT,
    job_industry_category TEXT,
    wealth_segment TEXT,
    deceased_indicator TEXT,
    owns_car TEXT,
    address TEXT,
    postcode TEXT,
    state TEXT,
    country TEXT,
    property_valuation INTEGER
);

-- Создание таблицы transaction
CREATE TABLE transaction (
    transaction_id SERIAL PRIMARY KEY,
    product_id INTEGER,
    customer_id INTEGER REFERENCES customer(customer_id),
    transaction_date DATE,
    online_order BOOLEAN,
    order_status TEXT,
    brand TEXT,
    product_line TEXT,
    product_class TEXT,
    product_size TEXT,
    list_price NUMERIC,
    standard_cost NUMERIC
);
```


## SQL-запросы

### Запрос 1: Уникальные бренды со стандартной стоимостью выше 1500
```sql
SELECT DISTINCT brand FROM transaction WHERE standard_cost > 1500;
```

### Запрос 2: Подтвержденные транзакции за период
```sql
SELECT * FROM transaction WHERE order_status = 'Confirmed' AND transaction_date BETWEEN '2017-04-01' AND '2017-04-09';
```

### Запрос 3: Профессии в IT и Financial Services, начинающиеся с "Senior"
```sql
SELECT DISTINCT job_title FROM customer WHERE job_industry_category IN ('IT', 'Financial Services') AND job_title LIKE 'Senior%';
```

### Запрос 4: Бренды, которые закупают клиенты из Financial Services
```sql
SELECT DISTINCT t.brand FROM transaction t JOIN customer c ON t.customer_id = c.customer_id WHERE c.job_industry_category = 'Financial Services';
```

### Запрос 5: 10 клиентов, заказавших онлайн продукцию из указанных брендов
```sql
SELECT DISTINCT c.* FROM customer c JOIN transaction t ON c.customer_id = t.customer_id WHERE t.online_order = TRUE AND t.brand IN ('Giant Bicycles', 'Norco Bicycles', 'Trek Bicycles') LIMIT 10;
```

### Запрос 6: Клиенты без транзакций
```sql
SELECT * FROM customer WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM transaction);
```

### Запрос 7: Клиенты из IT с транзакциями на максимальную стандартную стоимость
```sql
SELECT c.* FROM customer c JOIN transaction t ON c.customer_id = t.customer_id WHERE c.job_industry_category = 'IT' AND t.standard_cost = (SELECT MAX(standard_cost) FROM transaction);
```

### Запрос 8: Клиенты из IT и Health с подтвержденными транзакциями за указанный период
```sql
SELECT DISTINCT c.* FROM customer c JOIN transaction t ON c.customer_id = t.customer_id WHERE c.job_industry_category IN ('IT', 'Health') AND t.order_status = 'Confirmed' AND t.transaction_date BETWEEN '2017-07-07' AND '2017-07-17';
```
