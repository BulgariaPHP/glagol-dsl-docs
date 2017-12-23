Introduction
============

What is Glagol DSL?
-------------------
Glagol DSL is a domain specific language with the goal to help developers engineer microservices using the Domain-Driven Design [#evans2004domain]_ concepts out-of-the-box. To achieve this, Glagol DSL incorporates object-oriented structures that are much closer to the concepts of Model-Driven Design [#mdd]_ than the usual object-oriented languages. Additionally, Glagol also supports basic web (api) framework capabilities like routing, request handling and controllers.

Glagol DSL includes grammatical declarations for Entities, Value Objects, Repositories and Controllers. In general, all of those are embedded into the language's syntax and their usage implies following rules based on the concept behind each of them.

How does Glagol DSL work?
-------------------------
Glagol DSL is a transpiler and the output language is `PHP <https://php.net>`_. Simply put, the Glagol DSL sources (*.g) are parsed, type-checked and then compiled into PHP source code. To achieve most of its functional capabilities the Glagol DSL environment relies strongly on the `Lumen framework from Laravel <https://lumen.laravel.com/>`_ and the `Doctrine 2 ORM <http://doctrine-project.org/projects/orm.html>`_.

Architecturally, Glagol DSL is a simple client-server application. The server app is the actually compiling your sources and the client is used to send compile requests and more. This approach provides performance benefits and it also allows the compiler to run over a network (Compiler-as-a-service).

Under the hood
--------------
Glagol DSL is mainly written on `Rascal MPL <http://www.rascal-mpl.org/>`_ for the server app and Java for the client app.


.. rubric:: Footnotes

.. [#evans2004domain] Domain-driven design: Tackling complexity in the heart of software, Eric Evans, 2004, Addison-Wesley Professional.
.. [#mdd] Model-Driven Design is a set of design patterns and part of Domain-Driven Design as described by Eric Evans [#evans2004domain]_