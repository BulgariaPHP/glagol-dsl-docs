Introduction
============

What is Glagol DSL?
-------------------
Glagol DSL is a domain specific language with the goal to help developers engineer microservices using the Domain-Driven Design concepts out-of-the-box. To achieve this, Glagol DSL incorporates object-oriented structures that are much closer to the concepts of Model-Driven Design (a set of patterns from Domain-Driven Design) than the usual object-oriented languages. Additionally, Glagol also supports basic web (api) framework capabilities like routing, request handling and controllers.

Glagol DSL includes grammatical declarations for Entities, Value Objects, Repositories and Controllers. In general, all of those are embedded into the language's syntax and their usage implies following rules based on the concept behind each of them.

How does Glagol DSL work?
-------------------------
Glagol DSL is a transpiler and the output language is PHP. Simply put, the Glagol DSL sources (*.g) are parsed, type-checked and then compiled into PHP source code. To achieve most of its functional the Glagol DSL environment relies strongly on the Lumen framework from Laravel and the Doctrine 2 ORM.

Architecturally, Glagol DSL is a simple client-server application. The server app is the actually compiling your sources and the client is used to send compile requests and more. This approach provides performance benefits and it also allows the compiler to run over a network (Compiler-as-a-service).

Under the hood
--------------
Glagol DSL is mainly written on Rascal MPL for the server app and Java for the client app.

