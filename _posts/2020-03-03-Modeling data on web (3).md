---
layout: post
title: "Modeling data on web (3)"
date: 2020-03-03 
description: "python"
tag: 博客
---  

# Modelling Data on the Web Week note 3 Tree Data Models

## Tree data models

1. Data Structure formalisms: XML (including name spaces)
2. Schema Language: RelaxNG
3. Data Manipulation: XPath, DOM and Python

## XML

- a data model with a tree-shaped internal representation（设计它不是为了规划文档的布局 html 干这个）
- another formalism for the representation of semi-structured data
- was designed to be simple, generic, and extensible
- W3C standard
- simple, generic, extensible

## An XML document is a piece of text that

- describes structure and data
- has **internal representation** of a tree: DOM tree or infoset
- can be divided into smaller pieces called elements
  - contains elements
  - contain text/data
  - have attributes

## XML Elements

- elements are delimited(分隔) by tags e.g, \<people>
- tags are case-sensitive(大小写敏感)
- attributes(在标签中，比如 x="y") specify properties of an element. arbitrary number (任意数量) of attributes is allowed
- 同一个xml中可以出现多个相同名字的element

```xml
<element-name attr1-name="attr1-value" ... attrn-name="attrn-value">
    content
</element-name>
```

- each attr-name occurs at most once in one element
- content can be empty,  (simple content: text) and/or one or more elements
- the same element name can occur many times in a document
- an empty element can be abbreviated as(空内容的元素可以写成)

```xml
<element-name attr-decl1 ... attr-decln/>
```

## Well-formed of XML document

- exactly one root element
- tags, <, and > are correct
- tags are properly nested(正确嵌套)
- **attributes are unique for each tag and attribute values arequoted** 同一个tag中属性名不重复，value被双引号引用
- no comments inside tags

Well-formedness is a very weak property: Basically, it only ensures that we can *parse a document into a tree*(tree is the XML document's internal representation)

## DOM an API and an internal Representation for XML

### internal Representation for XML 的好处

- 更容易想到 structure：dcoument, element, nodes
- 忽略whitespace问题
- implementing software that handles XML easier
- specifying schema languages, other formalisms around it easier

### Parsing & Serializing XML documents

- parser: reads & analyses XML document. may generate parse tree (eg. dom tree withnodeslabelled with – tags, text content, and attributes and their values
- serializer: takes a data structure, e.g., some trees. generates an XML document

## Self-Describing in XML

XML is said to be self-describing

*From the external representation one should be able to derive the corresponding internal representation.*

External: the XML document, i.e., text!

Internal: the DOM tree

## Why need Schema for XML

A schema describes aspects of documents:

- what’s legal:
what a document can/may contain
- what’s expected:
what a document must contain
- what’sassumed:  default values

为什么使用xml schema: 2种情况。一是用它来描述文档给别人看，二是防止程序使用错误的文档（通过error checking）

## RelaxNG,a very powerful schema language for XML (week3 P59)

- 比XML Schema简单
- describle XML documents in terms of their tree abstractions: no entity declaration, no key constrains
- easy to use and reliable (simple and flexible)

### validation in RelaxNG

(为什么我们要验证XML document)

- ensure that structure is ok
- ensure value are correct data type
- generate PSVI to work with
- check constraints
- check other integrity constraints
- check constraints on elements/their value against external data

RelaxNG was designed to validate structure and link to datatype validators to type check values of elements/attributes

### basic principles

Three kind of *pattern* : text, attribute, element

可以使用 正则表达式： +, ?, \*, |, ,

```
grammar {
    start =
        element name {
            element first { text },
            element last { text }
}}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<name>
    <first>Harry</first>
    <last>Potter</last>
</name>
```

RelaxNG compact syntax

```xml
<?xml version="1.0" encoding="UTF-8"?>
<people>
    <person age="41">
        <name>
            <first>Harry</first>
            <last>Potter</last>
        </name>
        <address>4 Main Road </address>
        <project type="epsrc" id="1">DeCompO </project>
        <project type="eu" id="3">TONES </project>
    </person>
    <person>....
</people>
```

```
grammar { start = people-element

people-element = element people
        { person-element+ }

person-element = element person {
                attribute age { text },
                name-element,
                address-element+,
                project-element✱}

name-element = element name {
                element first { text },
                element middle { text }?,
                element last { text } }

address-element = element address { text }

project-element = element project {
                attribute type { text },
                attribute id {text},
                text }}
```
或者这样的：
```
grammar { start = 
        element people {people-content}

people-content =
        element person { person-content }+

person-content = attribute age { text },
        element name {name-content},
        element address { text }+,
        element project {project-content}*

name-content = element first { text },
        element middle { text }?,
        element last { text }

project-content = attribute type { text },
        attribute id {text},
        text }
```

## Manipulation of XML documents

- XPath (for a well-formed xml document)
- XQuery
- XSLT
- contrast this with DOM and SAX

## Xpath (理解即可)

使用路径表达
XPath operates on the abstract, logical tree structure of an XML document, rather than its surface, text syntax. But not on its DOMtree!.

!!Xpath data model is different from DOM data model (week3 P92)

### Xpath data model

It has following concepts:

- nodes: element, attri, txt, namespace,document(root)
- atomic value: e.g. xsd:string
- item: atomic values or nodes

## XML Namespace

- 为tag给一个前缀(可以不加)，如 xmlns:calc 其实 xmlns是前缀，calc是 local name
- 在tag后面给定命名空间的声明，如 xmlns:calc="http://bjp.org/calc/"
- Namespacename,e.g.,http://bjp.org/calc/
- 作用域（scope）范围: 与声明一同出现的元素，该元素的后代（在该元素中的元素）另给命名空间的后代除外。
- 当命名空间为默认命名空间时候，如

```xml
<plus xmlns="http://bjp.org/calc/">
</plus>
```

该tag下面所有的tags都使用该默认的命名空间，除非下面的tags自己定义的了命名空间

- 默认名称空间声明适用于其范围内的所有非前缀元素名称。
- *默认名称空间声明不能直接应用于属性名称；*
- 无前缀属性的解释取决于它们出现的元素

### example

```xml
<a:expression xmlns="foo1" xmlns:a="foo2" xmlns:b="bah">
    <b:plus xmlns:a="foobah">
        <int value="3"/>
        <a:int value="3"/>
    </b:plus>
</a:expression>
```

The ns of expression is: foo2
The ns of plus is: bah
The ns of first int is: foo1
The ns of second int is: foobah
