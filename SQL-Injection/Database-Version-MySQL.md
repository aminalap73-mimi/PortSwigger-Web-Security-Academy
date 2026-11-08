# SQL Injection – Querying the Database Type and Version (MySQL)


## 1. Lab Objective

This lab contains a SQL injection vulnerability in the product category filter. 
You can use a UNION attack to retrieve the results from an injected query.

## 2. Determine the Number of Columns

**Payload:**

```sql
'+ORDER+BY+2--+-
```

**Explanation:**
- `'` – Closes the original SQL string.
- `ORDER BY` – Sorts the query results.
- `2` – Refers to the second column.
- `-- -` – Comments out the remaining SQL statement.

**Observation:**

The original query contains **2 columns**.

## 3. Identify Columns That Accept Text

**Payload:**

```sql
' UNION SELECT 'a','a' FROM DUAL
```

**Explanation:**
- `UNION` – Combines the results of two SQL queries.
- `SELECT` – Retrieves data from the database.
- `'a'` – A test string used to check text compatibility.
- `FROM DUAL` – Uses MySQL's DUAL table.

**Purpose:**

Identify which columns can accept and display text values.

## 4. Retrieve the Database Version

**Payload:**

```sql
' UNION SELECT @@version,NULL#
```

**Explanation:**
- `UNION SELECT` – Adds an injected query to the original query.
- `@@version` – Retrieves the MySQL database version.
- `NULL` – Fills the second column.
- `#` – Starts a MySQL comment.

**Purpose:**

Retrieve information about the database version.

## 5. Key Concepts Learned

| Concept | Purpose |
|---|---|
| `ORDER BY` | Determine the number of columns |
| `UNION SELECT` | Combine query results |
| `NULL` | Match the number of columns |
| `@@version` | Retrieve MySQL version |
| `--` | Start an SQL comment |
| `#` | Start a MySQL comment |

## 6. Learning Outcomes

After completing this lab, I learned:

1. How SQL injection vulnerabilities can be exploited through product category filters.
2. How to determine the number of columns using `ORDER BY`.
3. How to use UNION-based SQL injection.
4. How to identify columns that accept text.
5. How to retrieve database information using MySQL system variables.





**Note:** Payloads are documented for educational purposes and authorized security testing.
