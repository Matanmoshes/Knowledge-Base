# SPL2 Documentation

## Introduction to SPL2

Splunk Processing Language version 2 (SPL2) is an evolution of the original SPL, designed to make data querying and manipulation more intuitive and powerful. SPL2 supports both traditional SPL syntax and SQL-like syntax, providing flexibility for users familiar with either language. This documentation covers the fundamental aspects of SPL2 syntax, helping you understand how to construct effective searches and commands within Splunk.

---
## Understanding SPL2 Syntax

### SPL2 Supports Both SPL and SQL Syntax

One of the key features of SPL2 is its bimodal nature—it supports both SPL and SQL-like syntax. This allows users to write queries in the style they are most comfortable with.

**Example using SPL syntax:**

```spl
search index=sec_ops sourcetype=crowdstrike:events:external
| stats max(alert_score) AS score BY endpoint, host
| where endpoint != "_*"
| eval verified=if(alert_score=*, 1, 0)
| fields verified score endpoint host
```

**Equivalent search using SQL syntax:**

```sql
SELECT MAX(alert_score) AS score, IF(alert_score LIKE "%", 1, 0) AS verified
FROM sec_ops
WHERE sourcetype = 'crowdstrike:events:external' AND endpoint NOT LIKE '_%'
GROUP BY verified, score, endpoint, host
```

---
## Command Syntax Elements

### Required and Optional Arguments

- **Required arguments** are essential for a command to function and are enclosed in angle brackets `< >`.
- **Optional arguments** enhance command functionality but are not mandatory, enclosed in square brackets `[ ]`.

**Example:**

For the `bin` command:

```spl
bin [<bin-options>...] <field> [AS <newfield>]
```

- `<field>` is required.
- `[<bin-options>...]` and `[AS <newfield>]` are optional.

### User Input Arguments

Commands often require user-provided values, such as strings or field names.

**Example for the `replace` command:**

```spl
replace (<string> WITH <string>)... [IN <field-list>]
```

- `<string>` and `<field-list>` are user input arguments.

### Repeating Arguments

An ellipsis `...` indicates that an argument can be repeated multiple times.

**Example for the `eval` command:**

```spl
eval <field>=<expression>[, <field>=<expression>]...
```

You can define multiple field expressions, separated by commas:

```spl
eval field1=expression1, field2=expression2, field3=expression3
```

### Grouped Arguments

Parentheses `( )` group arguments that are used together or represent alternatives.

**Example for the `GROUP BY` clause:**

```spl
[ (GROUP BY | GROUPBY | BY) [<field>[,<field>...]] 
  | (SPAN <field>,<int><timescale> | <field> SPAN=<int><timescale>) ]
```

- `(GROUP BY | GROUPBY | BY)` means you can use any of these keywords.
- `(SPAN <field>,<int><timescale> | <field> SPAN=<int><timescale>)` shows two ways to specify the span.

### Keywords

Keywords are terms that have special meaning in SPL2 commands, such as `AS` and `BY`. They are typically written in uppercase for readability but are case-insensitive.

**Example:**

```spl
stats count BY host
```

### Renaming Fields

Use the `AS` keyword to rename a field within a command.

**Example:**

```spl
stats max(delay) AS MaxDelay BY host
```

### Quoted Elements

Elements that must be included literally in your syntax are enclosed in quotation marks.

**Example for the `head` command:**

```spl
head [keeplast=(true | false)] [null=(true | false)] [while "(<boolean-expression>)"] [N]
```

- The parentheses around `<boolean-expression>` must be included in your search.

**Usage example:**

```spl
... | head while (counter < 100)
```

---
## Syntax Descriptions

In command documentation, arguments are listed alphabetically under Required and Optional sections. Each argument includes:

- **Syntax:** How to use the argument.
- **Description:** What the argument does.
- **Default (Optional arguments only):** The default behavior if the argument is not specified.

---
## Logical Operators

Logical operators are used in expressions to evaluate conditions, returning `TRUE` or `FALSE`.

- **Supported logical operators:**
  - `AND`
  - `OR`
  - `NOT`
  - `XOR`

**Usage example:**

```spl
where status="error" AND severity > 3
```

Operators must be written in uppercase when included in command syntax.

---
## BY Clauses

- **`BY` clause:** Groups results by specified fields, returning one row per unique combination.
- **`split-by` clause:** Splits results into separate columns based on field values.

**Note:** Wildcard characters (`*`) are not accepted in `BY` clauses.

---
## Fields and Wildcard Fields

When specifying fields:

- Use `<field>` to denote a single field.
- Field names can often be used directly, but certain characters require special handling.

### Field Names and Quotation Marks

- Field names starting with characters other than `a-z`, `A-Z`, or `_` must be enclosed in single quotes `' '`.
- Field names containing characters other than `a-z`, `A-Z`, `0-9`, or `_` must be enclosed in single quotes.

**Examples:**

- Field name with a space: `'field name'`
- Field name with a dash: `'field-name'`

---
## Reserved Words

SPL2 has reserved words that cannot be used as identifiers (e.g., field names) unless enclosed in single quotes.

**Examples of reserved words:**

```
AND, AS, BY, FROM, GROUP, WHERE, SELECT, ORDER, LIMIT, JOIN, ON, NOT, NULL, OR
```

**Using a reserved word as a field name:**

```spl
... | rename 'order' AS order_field
```

---
## Examples and Usage

### Using Required and Optional Arguments

**Bin Command Example:**

```spl
bin duration AS TimeBucket
```

- `duration` is the required field.
- `AS TimeBucket` is an optional argument that renames the binned field.

### Using Repeating Arguments with Eval

**Eval Command Example:**

```spl
eval total=price*quantity, discount=total*0.1, final=total-discount
```

- Multiple field expressions are calculated and assigned.

### Grouping and Logical Operators

**Search with Logical Operators:**

```spl
search (status="failure" OR status="error") AND severity > 2
```

- Groups conditions to combine logical evaluations.

### Handling Field Names

**Field Names with Special Characters:**

```spl
stats count BY 'user-id'
```

- `'user-id'` is enclosed in single quotes due to the dash.

### Reserved Words in Field Names

**Using Reserved Word as Field Name:**

```spl
... | rename 'select' AS select_field
```

- `'select'` is enclosed in single quotes to use as a field name.

