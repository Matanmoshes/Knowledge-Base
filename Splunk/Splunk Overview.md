# Splunk Overview Documentation

Splunk is a powerful platform designed for collecting, indexing, and analyzing machine-generated data in real-time. It enables organizations to search, monitor, and visualize data from various sources, making it essential for IT operations, security, and business analytics.

---
## Concepts

### Events

An **event** is the core unit of data in Splunk, representing a single occurrence within a specific timestamp. Events can be single or multi-line entries, such as logs, configuration files, or error traces. For example, an event from a web server log might look like:

```
173.26.34.223 - - [01/Mar/2021:12:05:27 -0700] "GET /trade/app?action=logout HTTP/1.1" 200 2953
```

Events can be grouped into **transactions**, which represent a sequence of related events spanning a duration, such as all actions in a user session.

### Metrics

A **metric** is a numerical measurement captured at a specific point in time, often associated with dimensions that provide additional context. Metrics are optimized for storage and retrieval, separate from events. Example metric data point:

- **Timestamp**: `08-05-2020 16:26:42.025-0700`
- **Measurements**:
  - `os.cpu.user=42.12`
  - `max.size.kb=345`
- **Dimensions**: `hq=us-west-1`, `group=queue`, `name=azd`

### Host, Source, and Source Type

- **Host**: The originating device of an event.
- **Source**: The specific file, stream, or input where an event comes from.
- **Source Type**: A classification of data inputs, like `access_combined` for web logs or `linux_syslog` for Linux system logs.

### Fields

**Fields** are key-value pairs extracted from events, enabling more precise searches and data manipulation. They can be defined automatically or using the Field Extractor tool with regular expressions or delimiters.

### Tags

**Tags** are labels assigned to field values, hosts, sources, or source types. They help group related events and simplify complex searches.

### Index-Time and Search-Time

- **Index-Time**: When data is ingested, parsed into events, and stored in indexes.
- **Search-Time**: When stored events are retrieved, and additional fields are extracted for analysis.

### Indexes

An **index** is a repository where Splunk stores data. You can create multiple indexes to organize data logically. By default, data goes into the `main` index.

---
## Core Features

### Search

The primary way to interact with data in Splunk is through **searches** using the Search Processing Language (SPL). Searches can:

- Retrieve events based on criteria.
- Calculate statistics and generate reports.
- Identify patterns and trends.
- Predict future behaviors.

### Reports

**Reports** are saved searches that can be run on-demand or scheduled. They can generate visualizations and alerts based on search results.

### Dashboards

**Dashboards** consist of panels displaying data visualizations, search results, or inputs. They provide real-time insights and can be customized per user or team needs.

### Alerts

**Alerts** monitor for specific conditions and trigger actions like sending emails or executing scripts when those conditions are met.

---

## Additional Features

### Datasets

Splunk supports various **datasets** for specialized analysis:

- **Lookups**: External tables that enrich event data.
- **Data Models**: Hierarchical structures defining semantic relationships, often used with Pivot for report generation.
- **Table Datasets**: Curated data collections for specific business purposes.

### Data Models

**Data Models** organize data hierarchically for optimized reporting and can be accelerated for performance improvements.

### Apps

**Apps** are packages extending Splunk's functionality, providing custom configurations, dashboards, and knowledge objects for specific use cases or teams.

### Distributed Search

**Distributed Search** allows scaling by separating search management (search heads) from data indexing (indexers), improving performance and facilitating data segregation.

---
## System Components

### Forwarders

**Forwarders** collect data and forward it to indexers. They are lightweight and ideal for data collection from various sources.

### Indexers

**Indexers** process incoming data from forwarders, transforming it into searchable events stored in indexes. They also handle search requests by retrieving data.

### Search Heads

**Search Heads** provide the interface for users to search, analyze, and visualize data. In distributed environments, they coordinate searches across multiple indexers.

---
## Search Processing Language (SPL)

SPL is a powerful language for searching and manipulating data in Splunk.

### Basic Syntax

Search commands are piped together, where each command processes the output of the previous one:

```
search criteria | command1 args | command2 args | ...
```

An implicit `search` command initiates the search pipeline.

### Example

```
sourcetype=access_combined error | top 5 uri
```

This search retrieves web access events containing "error" and displays the top 5 URIs.

### Time Modifiers

Specify time ranges using `earliest` and `latest`:

```
search error earliest=-1d@d latest=now
```

- `-1d@d`: 1 day ago, at the start of the day.
- `now`: Current time.

### Subsearches

Subsearches run before the main search and provide results to it. They are enclosed in square brackets:

```
sourcetype=syslog [search login error | return 1 user]
```

This finds syslog events for the user who last encountered a login error.

---
## Optimizing Searches

- **Limit Data Scope**: Use specific indexes and narrow time ranges.
- **Filter Early**: Apply restrictive criteria at the beginning of the search.
- **Efficient Commands**: Utilize features like summary indexing and report acceleration.

---
## Machine Learning Capabilities

Splunk integrates machine learning through tools like the Machine Learning Toolkit, enabling:

- Anomaly detection.
- Predictive analytics.
- Advanced data modeling.

---
## SPL2

An updated version of the search language, **SPL2**, offers improved syntax and consistency, making it easier to write and understand searches.

## Common Search Commands

- **chart/timechart**: Generate charts.
- **dedup**: Remove duplicates.
- **eval**: Compute expressions.
- **fields**: Include or exclude fields.
- **head/tail**: Return the first or last N results.
- **lookup**: Enrich data from external sources.
- **rename**: Change field names.
- **rex**: Extract fields using regex.
- **search**: Filter events.
- **sort**: Order results.
- **stats**: Perform statistical calculations.
- **table**: Display specific fields.
- **top/rare**: Find most/least common values.
- **transaction**: Group related events.
- **where**: Filter using expressions.

## Common Eval Functions

Used with the `eval` command to compute values:

- **abs(X)**: Absolute value.
- **case(X, "Y", ...)**: Conditional evaluations.
- **if(X, Y, Z)**: If-else logic.
- **isnull(X)**: Check for nulls.
- **len(X)**: String length.
- **lower(X)**: Convert to lowercase.
- **strftime(X, Y)**: Format time.
- **substr(X, Y, Z)**: Substring extraction.

## Common Stats Functions

Used with `stats`, `chart`, `timechart`:

- **avg(X)**: Average.
- **count(X)**: Occurrences.
- **dc(X)**: Distinct count.
- **max(X)**: Maximum value.
- **min(X)**: Minimum value.
- **sum(X)**: Total sum.
- **stdev(X)**: Standard deviation.
- **values(X)**: List of unique values.

---
## Search Examples

### Filter Results

- **Retrieve recent errors**:

  ```
  index=main error earliest=-24h
  ```

- **Exclude specific status codes**:

  ```
  index=main status!=404
  ```

### Group Results

- **Group by transaction**:

  ```
  ... | transaction session_id maxspan=30s
  ```

### Reporting

- **Count events by host**:

  ```
  ... | stats count by host
  ```

- **Average CPU usage per minute**:

  ```
  ... | timechart span=1m avg(cpu_usage) by host
  ```

### Advanced Reporting

- **Add average duration to events**:

  ```
  ... | eventstats avg(duration) as avg_duration
  ```

- **Predict future counts**:

  ```
  ... | timechart count | predict count
  ```

---
## Regular Expressions (Regex)

Used for pattern matching and field extraction.

- **\d**: Digit.
- **\w**: Word character.
- **.**: Any character.
- **^**: Start of line.
- **$**: End of line.
- **[...]**: Character set.
- **(pattern)**: Capture group.

**Example**:

Extracting email addresses:

```
... | rex field=_raw "(?<email>\b[\w.%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b)"
```

---
## Time Formatting

Common date/time format specifiers:

- **%Y**: Year (e.g., 2021).
- **%m**: Month (01-12).
- **%d**: Day (01-31).
- **%H**: Hour (00-23).
- **%M**: Minute (00-59).
- **%S**: Second (00-59).

**Examples**:

- **ISO Date**: `%Y-%m-%d` → `2021-12-31`
- **Full Date**: `%B %d, %Y` → `December 31, 2021`

