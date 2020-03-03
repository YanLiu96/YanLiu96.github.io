---
layout: post
title: "Modeling data on web (1)"
date: 2020-03-03 
description: "python"
tag: 博客
---  

# modeling data on the web note 1

## what is a (core) data model

1. flat: CSV(format是text file), word
2. table: relational table（table is the core data structure）
3. tree based: XML
4. graph based: RDF

## Data structure

1. unstructured data e.g., image, video, text. These data is informationally opaque（不透明）
2. semi-structured data e.g. JSON, XML. These data is informationally transparent(透明)

## key aspects of (what is ) data model

1. Underlying Data Structure,"CoreDataModel"
2. Data Integrity(完整性)
3. Data Manipulation(操作)
4. Data sharing

- a complete data representation and manipulation approach
- a particular data representation for a domain or application,also called the domain model

## data

different shapes

- arrary-like
- tree-like
- Graph-like
- Document-like

different volumes

- small to big

different velocities(速度)

- Static/offline(离线) to streaming(流)

different use patterns

- Many readers/few writers or the reverse or other

## polyglot persistence

use a variety of different data storage technologies for different kinds of data

首先考虑如何操作数据，然后才会确定哪种技术是最好的选择。应用程序使用不同的技术基于数据的使用方式来管理它们自己的数据。

即便是single core data model 也会有不同特征，多种domain models，版本。

## Tables in a relational model

- table is a set of tuples
- tuple is a set of key-value pairs

## 操纵CSV

```python
import csv
with open("../Adresses/mod2-uk-500.csv") as csvfile:
line_count = 0
myreader = csv.reader(csvfile, delimiter=',', quotechar='t') for row in myreader:
        if line_count == 0:
             line_count += 1
else:
print(f' Candidate {line_count}: Firstname {row[0]} Lastname {row[1]} City {row[4]}')
line_count += 1
print(f'Processed {line_count -1} Candidates.')
```

## what is formalism

- syntax: what can we write?
- semantics: what does our writing mean?
- with precise (mathematical) definitions

1. be clear about what we mean
2. allow the determination of key properties
3. to *abstract* away from particular implementions

## Modelling With SQL

- format: 不同的格式(表的设计(模式)不同 format就不同)
- schemas: create statements. eg create table t1. with certain constraints
- query: select statements. e.g. slect name form X
- SQL is (mostly) closed over tables (Functions 例外)

## Filtering in SQL

### Filtering Columns

Keep all columns:

```sql
SELECT * FROM People
```

Just a single column:

```sql
SELECT county FROM People
```

Multiple columns:

```sql
SELECT name, county FROM People
```

Rename columns:

```sql
SELECT street_address AS address FROM People
```

### Filtering rows

Selecting specific tuples

Equality:

```sql
SELECT * FROM People WHERE surname = "Smith"
```

Range:

```sql
SELECT * FROM People
WHERE heartrate > 95
```

Compound criteria:

```sql
SELECT * FROM People
WHERE heartrate > 95 AND county="Kent"
```

## Join in SQL

An inner join is a join filtered on common columns

```sql
SELECT * FROM People, Phone
INNER JOIN ON People.person_id = Phone.person_id
```

An outer join is like an inner join but it returns also rows that do not have a match in the other table （it will return also people who have no phone!）

```sql
SELECT * FROM People, Phone
RIGHT OUTER JOIN ON People.person_id = Phone.person_id
```

## Cost

- Considering lots of matches (think indexes) 
- Generating large intermediate tables

## NULL

null is a distinguished value which can mean:

- "Value not yet known"
- "Not applicable to this entity"
- "Value undefined"
- check out LSQL
- Key property: Unequal to everything

Null Unequal to everything So, Null can stand for

- an unknown value
- an attribute that is not applicable to the current row
- the empty set

If you have no nulls in your base tables
you can't get them in tables derived by inner join

So we need outer joins
