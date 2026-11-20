# SQL Injection – UNION Attack: Retrieving Multiple Values in a Single Column

## 1. Lab Objective

**Lab:** SQL injection UNION attack, retrieving multiple values in a single column.

The goal is to:
- Retrieve usernames and passwords from the `users` table.
- Combine two values into one column.
- Find the `administrator` credentials.
- Log in as the administrator.

## 2. Understanding the Database

The database contains a table called `users` with two columns:

| username | password |
|---|---|
| administrator | example_password |
| carlos | example_password |

The original query returns **two columns**, but only one column accepts text data.

This creates a problem: How can we retrieve both the username and password if only one column accepts text?

**Solution:** Combine both values into a single string using concatenation.

## 3. Step 1 – Determine the Number of Columns and Text Columns

Payload:

```sql
' UNION SELECT NULL,'abc'--
```

### Explanation

| SQL part | Meaning |
|---|---|
| `'` | Closes the original string |
| `UNION` | Combines the results of two SELECT queries |
| `SELECT` | Retrieves data |
| `NULL` | Represents an empty/unknown value |
| `'abc'` | Test string to identify a text-compatible column |
| `--` | Comments out the remaining SQL statement |

### Why use this payload?

- The original query returns two columns.
- The UNION query must also return two columns.
- `NULL` is used in the first column.
- `'abc'` tests whether the second column accepts text.

If `abc` appears in the response, the second column is displaying text.

## 4. Step 2 – Retrieve Multiple Values in One Column

Payload:

```sql
' UNION SELECT NULL,username||'~'||password FROM users--
```

### Explanation

| SQL part | Meaning |
|---|---|
| `UNION SELECT` | Combines the original query with another query |
| `NULL` | Placeholder for the first column |
| `username` | Retrieves the username |
| `||` | Concatenates (joins) strings in databases such as PostgreSQL and Oracle |
| `'~'` | Separator between the username and password |
| `password` | Retrieves the password |
| `FROM users` | Retrieves data from the users table |
| `--` | Comments out the remaining SQL statement |

### How does concatenation work?

Example:

```sql
username = 'administrator'
password = '12345'
```

Expression:

```sql
username || '~' || password
```

Result:

```text
administrator~12345
```

Both values are now displayed as one text value in the second column.

## 5. Why Use `~`?

The `~` symbol is a separator.

It helps distinguish the two values after concatenation.

Without a separator:

```text
administrator12345
```

With a separator:

```text
administrator~12345
```

The second result is easier to understand.

## 6. Why Not Retrieve Both Columns Separately?

This payload might fail:

```sql
UNION SELECT username,password FROM users
```

Reason:

The first column in the original query may not accept text.

Instead, combine the username and password into one text value:

```sql
username || '~' || password
```

This is the main idea behind this lab.

## 7. Important SQL Concepts

| Concept | Explanation |
|---|---|
| SQL | Structured Query Language |
| SQL Injection | Injecting SQL commands through unsafe user input |
| UNION | Combines results from multiple SELECT statements |
| SELECT | Retrieves data |
| FROM | Specifies the table |
| NULL | Represents an unknown or missing value |
| Concatenation | Joins multiple strings into one string |
| `||` | String concatenation operator in some DBMSs |
| `--` | SQL comment marker in supported databases |

## 8. Important UNION Rules

1. Both SELECT statements must return the same number of columns.
2. Corresponding columns must have compatible data types.
3. Column position matters.
4. `NULL` can be useful as a placeholder when testing column compatibility.
5. Database systems may use different concatenation operators and comment syntax.

## 9. General Method for Solving UNION SQL Injection Labs

1. Find the injection point.
2. Determine the number of columns.
3. Identify which columns accept text.
4. Identify the database type when necessary.
5. Identify the required table and column names.
6. Retrieve the required information.
7. Interpret the results and complete the lab objective.

## 10. Key Takeaway

**The main lesson:** When only one column can display text, multiple values can be combined into one string using concatenation.

Example:

```sql
username || '~' || password
```

This combines the username and password into one result.

---

## Quick Revision Questions

1. What does UNION do?
2. Why must both SELECT statements return the same number of columns?
3. What does `NULL` represent?
4. What is the purpose of `||`?
5. Why is `~` used in the payload?
6. What does `FROM users` specify?
7. Why might retrieving username and password in separate columns fail?

**Remember:** Understand what each SQL component does instead of memorizing the payload.
