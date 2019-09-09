+++
title = "Compilers"
author = ["Jethro Kuan"]
lastmod = 2019-09-09T15:46:29+08:00
draft = false
math = true
+++

## What are compilers? {#what-are-compilers}

Compilers are programs that read in one (source) language, and translate them into another (target) language.

Compilers need to perform two main tasks:

1.  Analysis: breaking up a source program into constituent parts, and create an intermediate representation
2.  Synthesis: Construct the desired target program from an intermediate representation

A compiler is often designed in a number of phases:

lexical analyses
: reads a stream of characters, and groups them into a stream of tokens (logically cohesive character sequences). This often requires maintaining a symbol table

syntax analyses
: imposes a hierarchical structure on the token stream (parse tree)

semantic analyses
: checks parse tree for semantic errors (e.g. type errors)

intermediate code generation
: a program for an abstract machine

Code optimization
: Optimizes the intermediate code representation in terms of efficiency


### Cousins of the Compiler {#cousins-of-the-compiler}

1.  Prepreprocessors
    1.  Macro Processing
    2.  File inclusion
    3.  Language Extension
2.  Assemblers
3.  Loaders and Link-editors


### Textbook {#textbook}

The Dragon book


## Lexical Analyses {#lexical-analyses}

-   Read the input characters of the source program
-   Group characters into lexemes
-   Output a sequence of tokens to the parser
-   Other tasks:
    -   Filter out comments and whitespace
    -   Correlating error messages with source program
    -   Constructing symbol tables

-   Separation of concerns lead to simplicity of design
-   Compiler efficiency is improved, when using special techniques


### Term Definitions {#term-definitions}

token
: <Token name, attribute value>

pattern
: A description of the formthat can be recognized as a token

lexeme
: A sequence of characters matching the pattern for a token

E.g. `printf("Total = %d", score);`

| Tokens      | Lexemes |
|-------------|---------|
| id          |         |
| literal     |         |
| punctuation |         |

```text
ID(printf)  LBRACE LITERAL("Total = %d") COMMA ID(score) RBRACE SEMI
```

Tokens have several categories:

| Keywords    | if else return |
|-------------|----------------|
| Operators   | > < <> != !    |
| Identifiers | pi score temp1 |
| Constants   | 3.14 "hello"   |
| Punctuation | ( ) ; :        |

Attributes for tokens distinguish two lexemes belonging to the same symbol table:

<number, 0>, <number, 1>, <price, ptr to symbol table>


## Syntax Definition {#syntax-definition}

We use [context-free grammars]({{< relref "theory_of_computation" >}}) to specify the syntax of a language.

A grammar for arithmetic expressions can be constructed from a table
showing the associativity and precedence of operators.

```text
left-associative: + -
left-associative: * /
```

Two different non-terminals can be constructed for the two levels of
precedence:

```text
factor -> digit | (expr)
term -> term * factor
  | term / factor
  | factor
expr -> expr + term
  | expr - term
  | term
```


## Syntax Directed Translation {#syntax-directed-translation}

Syntax-directed translation is done by attaching rules or program
fragments to productions in a grammar. e.g. consider

```text
expr -> expr_1 + term
```

We can translate expr by:

```text
translate expr_1;
translate term;
handle +;
```


## Parsing {#parsing}

Parsers use [pushdown automata]({{< relref "theory_of_computation" >}}) to do parsing.


### Recursive-Descent Parsing {#recursive-descent-parsing}


## Tools {#tools}


### Lex {#lex}

A Lex compiler takes a Lex source program, and outputs a program. This
program is compiled in its source language (originally C, jFlex for
Java). The result program takes an input stream as input, and produces
a sequence of tokens as output.

A lex program has the following form:

```text
declarations
%%
translation rules
%%
auxiliary functions
```

The declaration section includes declarations of variables, manifest
constants, and regular definitions.

The translation rules have form: Pattern { Action }.

Each pattern is a regular expression, which may use the definitions of
the declaration section. The actions are fragments of code.

The auxiliary functions section contains used in these actions.