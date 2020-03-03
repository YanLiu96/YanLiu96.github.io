---
layout: post
title: "Modeling data on web (4)"
date: 2020-03-03 
description: "python"
tag: 博客
---  

# Modelling Data on the Web note 4: XML Schema, XQuery, and robustness

## Tree data models

1. Data Structure formalisms: XML (including name spaces)
2. Schema Language: XML Schema (XSD)
3. Data Manipulation: XQuery

## XML 有很多schema。可以通过 expressive power, easy of use/understanding, the complexity of validating a document w.r.t. a schema 来比较下。 其中 XML schema more expressive,have ns,gave datatyoe than DTDS

## XML Schema another schema language for XML

an RNG schema in compact syntax is not a well-formed XML document

an XML Schema schema is a well-formed XML document

## XML schema example

```xml
<?xml version="1.0"?>
<note xmlns= "http://www.w3schools.com"
xmlns:xs= "http://www.w3.org/2001/XMLSchema"
xmlns:xsi= "http://www.w3.org/2001/XMLSchema-instance">
    <to>Tove</to>
    <from>Jani</from>
    <sentOn>2007-01-29</sentOn>
    <body>
        Have a nice weekend!
    </body>
</note>
```

```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"  targetNamespace= "http://www.w3schools.com"
xmlns="http://www.w3schools.com" elementFormDefault="unqualified"> <xs:element name="note">
    <xs:complexType>
        <xs:sequence>
            <xs:element name="to" type="xs:string"/>
            <xs:element name="from" type="xs:string"/>
            <xs:element name="sentOn" type="xs:date"/>
            <xs:element name="body" type="xs:string"/>
        </xs:sequence>
    </xs:complexType>
</xs:element>
</xs:schema>
```

## XML Schema 注意点

XML Schema provides support for modularity & re-use through 

- xs:import
- xs:include
- xs:redefine

An XSD schema typically has 2 namespaces:

1. targetNamespace for **elements** which created by us.
2. XML Schema namespace http://www.w3.org/2001/XMLSchema

## XML Schema types

- xs:simpleType: attribute values and elements
- xs:complexType: elements with
• element content or
• mixed element contentor • text content and attributes

mixed content:

```xml
<xs:complexType name="PersonType" mixed="true">
    <xs:sequence>
        <xs:element name="Name" type="xs:string"/>
        <xs:element name="DoB" type="xs:date"/>
</xs:sequence>
<xs:attribute name="friend" type="xs:boolean" default="true"/>
<xs:attribute name="phone" type="xs:string"/>
</xs:complexType>
```

XML Schema example2:

```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://www.w3schools.com"
elementFormDefault="unqualified">
    <xs:element name="note">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="to" type="xs:string"/>
                <xs:element name="from" type="xs:string"/>
                <xs:element name="sentOn" type="xs:date"/>
                <xs:element name="body" type="xs:string"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
</xs:schema>
```

### XML Schema: types

- xs:simpleType: attribute value, elements
- xs:complexType: elements with element content or mixed element content, text content and attri.

### XML Schema: Atomic simple types

can be restricted using xs:restriction facets:

- enumeration:

```xml
<xs:simpleType name="bikeType">
    <xs:restriction base="xs:string">
        <xs:enumeration value="MTB"/>
        <xs:enumeration value="Race"/>
        <xs:enumeration value=“Children”/>
        <xs:enumeration value=“Hybrid"/>
        <xs:enumeration value=“Folding"/>
    </xs:restriction>
</xs:simpleType>
```

- length

```xml
<xs:simpleType name="eightChar">
    <xs:restriction base="xs:string">
        <xs:length value="8"/>
    </xs:restriction>
</xs:simpleType>
```

### Schemas and Types； PSVI

1. Post-schema-validation infoset: Internal Rep. adorned（带有） with schema information e.g., a tree adorned with default values & types （带有模式信息的内部代表）
2. PSVI = DOM for XML document + XSD schema

## Compare  XML Schema & RelaxNG

In RNG, no restrictions & extension, no (non-atomic) types

XML Schema has restrictions on expressing constraints on content models

## Self-Describing

XML is an external format for representing data

It's Self-describing

- Destroyed by external validation,
- i.e., using application-specific schema for validation

## XQuery

XPath, XQuery is based on node sequences

```xml
<?xml version="1.0" encoding="UTF-8"?>
<contactlist>
    <person categ="friend" age="25">
        <name>
            <lastname>Doe</lastname>
            <firstname>John</firstname>
        </name>
        <phone>0044 161 1234 5667</phone>
        <address> 123 Main Street</address>
        <city>Manchester</city>
    </person>
```

```xquery
for $b in
    doc("people.xml")/contactlist/person
let $name as text() :=
    if (xs:integer($b/@age) < xs:integer(16))
        then ($b/name/firstname/text())
        else ($b/name/lastname/text())
return $name
```

```xquery
<sales>
{for $p in doc("contactlist-john-doe.xml")/contactlist/person
    for $c in doc("cities.xml")/citylist/city
    where $p/city = $c/name
    return
    (for $i in 1 to fn:count($c/club)
     return concat("Dear ", $p/name/firstname,", do you like ", $c/club[$i], " ?"))}
</sales>
```

## Quick Note on PSVI

Post Schema-Validation Infoset

- First approximation: DOM + Schema Information
- Remember node types in the DOM

PSVIs are known to be valid!

1. Schema + Well formed XML => Validation=>PSVI
2. Schema(create) + CSV => program => Populated database

## error handling

### Postel’s Law

1. Be liberal in what you accept,
2. and conservative in what you send.

### But error handing 对于接受发送都 strict

## Schemas

In SQL, schema before all

- CREATE TABLE or nothing happens
- Can’t INSERT INTO
- Can’t SELECT FROM
- So every SQL database has a schema

XML,never need a schema except the minimal schema of well-formed-ness
所以为什么有schema呢：

- To communicate
- To error check
- to guide tools
