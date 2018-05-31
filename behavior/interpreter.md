# Interpreter

## Introduction: {#introduction}

Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language.

{% hint style="info" %}
The Interpreter Pattern is widely used in the compilers implemented with object oriented languages.
{% endhint %}

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​When there is a language need to be interpreted or parsed, and you can represent statements in the language as abstract syntax trees.
* **The grammar is simple**; if the grammar becomes complex, it will cause the class hierarchy unmanageable.
* **The efficiency is not a critical concern**;  The most efficient interpreters are usually not implemented by interpreting parse trees directly but by first translating them into another form.

## Component: {#component}

​

![](../.gitbook/assets/image.png)

#### Abstract Expression:

* Declares an abstract Interpret operation that is common to all nodes in the abstract syntax tree.

#### Terminal Expression:

* Implements an Interpret operation associated with terminal symbols in the grammar.
* An instance is required for every terminal symbol in a sentence.

#### Nonterminal Expression:

* One such class is required for every rule in the grammar.
* Maintains instance variables of type Abstract Expression for each of the symbols.
* Implements an Interpret operation for nonterminal symbols in the grammar Interpret typically calls itself recursively on the variables.

#### Context:

* Contains information that's global to the interpreter.

#### Client:

* Builds an abstract syntax tree representing a particular sentence in the language that the grammar defines.
* Invokes the interpret operation.

## Code Example - Golang {#code-example-golang}

{% hint style="info" %}
The example use case is trying to return the answer for the user input like: 

`(1+2*3) - 10 / 100 * 1000 - 0.5 * 20 = ?`
{% endhint %}

```text
​​
```

**NOTE**:

[https://sourcemaking.com/design\_patterns/interpreter](https://sourcemaking.com/design_patterns/interpreter)

## ​Related Patterns:

#### Composite: 

The abstract syntax tree is an instance of the Composite Pattern.

#### Flyweight

Shows how to share some language symbol.

#### Iterator

Use the Iterator to traversal the structure.

#### Visitor

Can be used to maintain the behavior in each node in the abstract syntax tree in one class.

​

​

