---
layout: post
title: "Modeling data on web (5)"
date: 2020-03-03 
description: "python"
tag: 博客
---  

# Modelling Data on the Web note 5: More error handling & RDF, a graph-based DM

## Robustness

In computer science, robustness is the ability of a computer system to cope with errors during execution and cope with erroneous input.

## Formats for External Represent of data

A format consists of

1. a *core data model* (csv, table, XML, JSON,...)
2. a *conceptual model* (显性或隐性，如ER-Diagram)
3. *schema(s)* formalising/describing the format
    - documents describing(some aspects of our)design
    - e.g.,occupancy.rnc,occupancy.sch,...
4. the set of conforming *ExtReps*(e.g., XML documents)
    - concrete embodiments(具体体现) of our design

2 formats F1 = \<DS1, CM1, S1, D1>, F2 = <DS2, CM2, S2, D2>
it may be that

- S1 only captures some aspects of D1
- S1 is only a description in English
- D1=D2 but S1≠S2
- DS1=DS2 andCM1=CM2 but S1≠S2 andD1≠D2
- ...and that F1 makes better use of DS1’s features than DS2

When you design a format, you design each of its aspect and

- how much you make explicit
- how you formalise CM, S

## Schematron

### The essence of Schematron

- finer control (validate parts of a document)
- have high expressivity
- Essentially, code based validation!
- little declarative

- Friendly: combine Schematron with RelaxNG. (has two phase validation)
- Powerful: 在context report中支持Xpath

- 需要 well formed XML
- work on DOM

### what is Schematron

- Rule-based XML schema
- test oriented(有利于测试文档)
- Complimentary to other schema languages
- conceptually simple: a rule sets a context and contains
  - asserts (As) - act “when test is false”
  - reports (Rs) - act “when test is true”
- 可以做些别的模式不允许做的事情
- 要么passes all rules in a schema S 要么fails some of the rules in S

### shcematron example

```xml
<pattern>
    <rule context="person">
        <let name="F" value="@FirstName"/>
        <let name="L" value="@LastName"/>
        <assert test="count(//person[@FirstName = $F and @LastName = $L]) = 1">
            There can be only one person with a given name,
            but there is <value-of select="$F"/> <value-of select="$L"/> at least twice!
        </assert>
    </rule>
</pattern>
```

## core concepts of data model

### data models

shape: none, tables, trees, graphs,...

### core data modes for these above

- [tables] csv files, SQL tables
- [trees] sets of feature-value pairs, XML, JSON
- [graphs] RDF

### schema language for for these above

- [SQL tables] SQL
- [XML] RelaxNG, XSD, Schematron,...
- [JSON] JSON Schema

### manipulation mechanisms

- [SQL tables] SQL
- [XML] DOM, SAX, XQuery,...
- [JSON] JSON API,...

## Graph shaped Data Models

### External Representation for Graph

- Pictures are a **bad** external representation for graphs.
Because it captures loads of irrelevant information like colour, location.
- turtle
- N3
- N-triples
- JSON-LD

## RDF: a data structure formalisms for graphs

## RDFS: a schema language for RDF

(subject, predicate, object)

an RDF graph G is a set of triples {(si, pi, oi) | 1 ≤ i ≤ n} where each

- si ∈ U∪B
- pi ∈ U
- oi ∈ U∪B∪L
U: URIs (for resources), B: Blank nodes, L: Literals

## RDFS a schema language for RDF

- RDFS does not describe/constrain structure
- RDFS can’t be used to “validate” documents/graphs
**schemas describe ExtReps**

## What do schemas usually do

- schemas that describe ExtReps
- RDFS例外，它只能知道 what’s assumed/known, and thus – what can be inferred

## SPARQL: a query language for graphs

- Ask return a boolean
- Select return a table
- CONSTRUCT return graphs
- DESCRIBE return graphs

### example

```rdf
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex: <http://www.cs.man.ac.uk/> .

ex:bobthebuilder
    foaf:firstName "Bob";
    foaf:lastName "Builder"; foaf:knows ex:wendy ; foaf:knows ex:farmerpickles; foaf:knows ex:bijanparsia.
ex:wendy
    foaf:firstName "wendy";
    foaf:knows ex:farmerpickles.
ex:farmerpickles
    foaf:firstName "Farmer";
    foaf:lastName "Pickles";
    foaf:knows ex:bobthebuilder.
ex:bijanparsia
    foaf:firstName "Bijan";
    foaf:lastName "Parsia".
```

1. How many friends does Bob Builder have?

```sparql
SELECT DISTINCT COUNT(?friend)
WHERE {
    ex:bobthebuilder
    foaf:firstName "Bob";
    foaf:lastName "Builder";
    foaf:knows ?friend };
```

2. Give Bob Builder’s friends’ friends?

```sparql
 SELECT ?first, ?last
 WHERE {
    ex:bobthebuilder
    foaf:firstName "Bob";
    foaf:lastName "Builder";
    foaf:knows ?x.
    ?x foaf:knows ?y.
    ?y foaf:firstName ?first;
    foaf:lastName ?last}
```

3. Bob Builder’s friends’ friends

```sparql
SELECT ?first, ?last
WHERE {ex:bobthebuilder
        foaf:firstName "Bob";
        foaf:lastName "Builder";
        foaf:knows+ ?friend.
        ?friend foaf:firstName ?first;
        foaf:lastName ?last}
```
