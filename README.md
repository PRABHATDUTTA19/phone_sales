# 📊 Phone Market Analysis – SQL Project
## 1. Project Overview
This project analyzes smartphone market data from the phone_data table to extract insights on pricing, popularity, battery capacity, and brand trends.
It demonstrates core SQL skills:

Data filtering & sorting (SELECT, WHERE, ORDER BY)

Summarization & grouping (GROUP BY, Aggregate Functions)

Combining datasets (INNER JOIN, LEFT JOIN, RIGHT JOIN)

Advanced queries (Subqueries)

Analytical views (CREATE VIEW)

Query optimization (Indexes)

```sql
phone_data (
    sr_no SERIAL PRIMARY KEY,
    brand_name VARCHAR(255),
    model_name VARCHAR(255),
    os CHAR(50),
    popularity INTEGER,
    best_price INTEGER,
    lowest_price INTEGER,
    highest_price INTEGER,
    sellers_amount INTEGER,
    screen_size NUMERIC(4,2),
    memory_size FLOAT,
    battery_size INTEGER,
    release_date DATE
)
brand_info (
    brand_id SERIAL PRIMARY KEY,
    brand_name VARCHAR(255),
    headquarters_country VARCHAR(100)
)
```
# Analysis Tasks & Queries
## Goal: Identify large-screen, highly popular phones.
```sql
SELECT sr_no, brand_name, model_name, screen_size, popularity
FROM phone_data
WHERE screen_size > 6.00 AND popularity > 80
ORDER BY popularity DESC;
```
## Goal: Find the average price of phones per brand.
```sql
SELECT brand_name, AVG(best_price) AS avg_price
FROM phone_data
GROUP BY brand_name
ORDER BY avg_price DESC;
```
## Goal: Merge phone details with brand country.
```sql
-- INNER JOIN: Phones with matching brand info
SELECT p.brand_name, p.model_name, b.headquarters_country
FROM phone_data p
INNER JOIN brand_info b
ON p.brand_name = b.brand_name;

-- LEFT JOIN: All phones, even without brand info
SELECT p.brand_name, p.model_name, b.headquarters_country
FROM phone_data p
LEFT JOIN brand_info b
ON p.brand_name = b.brand_name;

-- RIGHT JOIN: All brands, even without phone listings
SELECT p.brand_name, p.model_name, b.headquarters_country
FROM phone_data p
RIGHT JOIN brand_info b
ON p.brand_name = b.brand_name;

```
## Goal: Get phones below the average market price.
```sql
SELECT sr_no, brand_name, model_name, best_price
FROM phone_data
WHERE best_price < (
    SELECT AVG(best_price) FROM phone_data
);
```
## Goal: Find the most popular phone of each brand.
```sql
SELECT brand_name, model_name, popularity
FROM phone_data p1
WHERE popularity = (
    SELECT MAX(popularity)
    FROM phone_data p2
    WHERE p1.brand_name = p2.brand_name
);
```
## Sum of all battery capacities by brand.
```sql
SELECT brand_name, SUM(battery_size) AS total_battery
FROM phone_data
GROUP BY brand_name;
```
## Goal: Average, max, and min price per OS.
```sql
SELECT os, AVG(best_price) AS avg_price,
       MAX(best_price) AS max_price,
       MIN(best_price) AS min_price
FROM phone_data
GROUP BY os;
```
## Goal: Create reusable views for reports.
```sql
CREATE VIEW avg_price_by_brand AS
SELECT brand_name, AVG(best_price) AS avg_price
FROM phone_data
GROUP BY brand_name;

CREATE VIEW top_popular_phones AS
SELECT brand_name, model_name, popularity
FROM phone_data
WHERE popularity > 90
ORDER BY popularity DESC;
```
## Goal: Improve query speed using indexes.
```sql
-- Index on brand_name for faster lookups
CREATE INDEX idx_brand_name ON phone_data(brand_name);

-- Index on popularity for quick top phone lookups
CREATE INDEX idx_popularity ON phone_data(popularity DESC);

-- Index on OS for OS-based reports
CREATE INDEX idx_os ON phone_data(os);

```

