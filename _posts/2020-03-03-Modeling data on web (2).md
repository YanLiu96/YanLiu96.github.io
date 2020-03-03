---
layout: post
title: "Modeling data on web (2)"
date: 2020-03-03 
description: "python"
tag: 博客
---  

# modeling data on the web note 2 Tree data models

## Tree Data Models

1. Data Structure formalisms: JSON
2. Schema Language: JSON Schema, CSVW
3. Data Manipulation: Python, JSON package

## semi-structured data models

当将数据存入RDBMS/TABLES中，将需要很多joins，递归（Recursive）joins.所以需要替代品: Semi-Structured Data Models

### Database Alternatives to Tables

Trees, underlying(基础) various semi-structured data models

- OEM
- Lore
- *JSON*
- *XML*

Graphs

## What is semi-structured data

is data organised in semantic entities, where

- similar entities aregrouped together
- entities in same group may not have same fields
- often defined as a possibly nested set of field-value pairs
- not all fields may be required
- carries its own description
- we can have several values for the same field

## serializing and parsing

serializing: Tree -> Bit (10011010)
parsing: Bit (10011010) -> Tree

## Internal Representation

We need an Internal Representation to know when two pieces of semi-structured data are the same, and to determine what matters

### a piece of semi-structure data/nested set of field-value pairs can be represtented as a tree.(tree就是一种半结构化数据的内部表示)

- leaf nodes: standing for single data items (eg. "Yan")
- inner nodes carry no label(树的内部节点什么也没有)
- edges labelled with field names（eg. fist_name）

we can represent relational data by SSD:semi-structure data(tree), though with an overhead.

## How to represent(store) SSD

various **formalisms** to store semi-structured data

- Object Exchange Model(OEM)
- Lore
- XML
- JSON

different **formalisms** with different

- internal representations
- mechanisms for self-describing
- datatypes (e.g., integer, Boolean, string, data-time...) supported
- description mechanisms for (semi) structure: schema languages
- query languages & manipulation mechanisms

## JSON: a tree-shaped / semi-structured data model / interchange format

1. JSON was developed to serialise(STORE) JavaScript objects
2. JS object or other data can be serialised into JSON, XML

JSON’s internal representation is

- objects {} and arrays [] with nesting
- atoms （numbers, booleans, strings）e.g. 1,ture,"hah"
- or similar structures in other programming languages

## Trees

Trees have many different shapes: for example, Abstract trees

## Self-Describing

eg, ExCSV(带heading的csv) make data more guessable. It could read the field tags  
and guess intent. But still need to guess the *type*.

**From the external representation one should be able to derive the corresponding internal representation.**

- External:the (Ex)CSV, JSON file/snippet, i.e., text!
- Internal:the JS object, arrary

**If one converts from an internal representation to the external representation and back again, the new internal representation should equal the old**
XML can be said to be more self-describing than ExCSV

## Schemas(用来约束数据类型)

A schema is a description of

1. **DBS.** It describes

- tables
- their names and their attributes
- keys, keyrefs
- integrity constraints

2. **CSVs.** It describes

- columns
- their value range (ie which data goes where)

3. **JSON documents.** It describes

- structure: how objects/vectors are nested, which keys are required
- data: datatypes,restrictions (what values go where)

4. **XML documents** It describes

- tag names
- attribute names
- structure: how elements are nested. which elements have which attributes
- data: what values (strings? numbers?) go where

### What scehmas can do (why use schemas)

A schema describes aspects of documents:

- what’s legal: what a document can/may contain

- what’s expected: what a document must contain

- what’sassumed: default values

Two modes for using a schema:

- **descriptive:** describing documents, let people know how to serialize their data
- **prescriptive:** prevent the application from using wrong documents

### Benefits of an schema

- Specification: according your format
- As input for applications. applications can *do error-checking in a format independent way* (eg. whether an XML document conforms to a schema)
(可以用validator tool来检查输入文本，no need to test for errors, 应用只需处理validator的结果，这样达到了format indepent)

### CSVW is a CSV Schema

CSVW supports built-in datatypes plus XML Schema (XSD) Data Types.

It can be used to describle(表，key约束，列名，列值，用正则表达式)

- a group of tables
- uniqueness and (foreign) keys constraints
- defaults values for cells
- column titles, possibly with language code
- use regular expressions to describe column titles or values
- use various vocabularies, e.g., Dublin Core
- units of measures that go with column

## Validation and Being Valid

A CSV D is **valid** wrt/against a schema S if D satisfies all constraints in S.
Checking whether D is valid wrt S is called **Validation** (which done by hand or validator)

D can be valid wrt S without any validation having taken place!

## Regular Expressions

Given a regular expression e, a string w matches e

- w为空字符串，w等于e，w等于e中的某个symbols
- if w=w1w2 and e=(e1,e2) and w1 matches e1 and w2 matches e2 , or
- ife=(e1|e2) and w matches e1 or w matches e2
- if w=𝜺 and e=e1*
- if w=w1 w2...wn and e=e1* and each wi matches e1
Hence we can use
- e+ as abbreviation for (e,e*) 匹配前面的子表达式一次或多次。
- e? as abbreviation for (e|ε) 匹配前面的子表达式零次或一次。
- \* 匹配前面的子表达式零次或多次

## JSON Schema (2nd schema language)

### types supported by json schema

- restrict String: max/min length, regular expression, date, time
- Numbers: integer or floats. (minnimum, exclusiveMinimum-不包括极限值)
- Objects{}
- Arrays[]: be restricted via size, type of (all, some, tuple) content
- Boolean
- Null

### examples

```json
{
"properties": {
        "id": {
                "description": "The unique identifier for a person",
                "type": “integer”,
                "minimum": 10,
        },
        "name": {
                "description": "Name of the person",
                "type": "object",
                "properties": {
                        "first": {"type": "string"},
                        "last": {"type": "string"},
                        "others": {"type": "string"}
                },
                "required": ["first", "last"],
                "additionalProperties": false 
        },
},
"required": ["id", "name"] }
```

```json
{"$schema": "http://json-schema.org/draft-04/schema#",
    "definitions": {
        "GoodString": {
             "description": "Strings for Names",
             "type": "string",
             "pattern": "[A-Z][a-z]+"}},
     "title": "Product",
     "description": "Uli's example",
     "type": "object",
     "properties": {
        "id": {"description": "The unique identifier for a person",
                "type": "integer"},
        "name": {"type": "object",
                 "properties": {
                     "first": {"$ref": "#/definitions/GoodString"},
                     "last":  {"$ref": "#/definitions/GoodString"},
                      "others":{"$ref": "#/definitions/GoodString"}},
                 "required": ["first", "last"],
                 "additionalProperties": false},
         "age": {"type": "number",
                 "minimum": 0,
                 "maximum": 100}},
     "required": ["id", "name","age"]}
```
