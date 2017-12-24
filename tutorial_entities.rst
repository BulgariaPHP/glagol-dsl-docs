.. _tutorial_entities:

First Entity
============
One the most fundamental patterns of enterprise software nowadays is the Entity. Entity is simply an object that is used to persist data and state during runtime. Furthermore, entities are usually distinguished by some form of identity. For example, in the case with a relational database table, an entity can be identified by a primary key field and its value.

Glagol DSL supports a built-in syntax declaration for entities that can be automatically bundled with database tables. Generally speaking, the purpose of having a built-in language structure for entities is to hide the underlying application logic from the developer and bring the focus on the entity as a business item itself.

Usually, entities are maintained with the help of the so called `Object-relational mapper` libraries. `Doctrine 2 ORM <doctrine-project.org>`_ is one of the popular ORMs in the PHP world. Therefore, it is being used by the Glagol DSL compiled codes. However, a Glagol DSL developer does not need to worry about ORMs because this part is taken care by the language and the compiler itself.

Create your first entity
------------------------
Lets create a Glagol DSL entity. Create a new package by making a directory named :code:`src/Article`. Next, put the following code inside::

    namespace Article

    entity Article {
        int id;
        string title;
        string contents;
        string author;
    }

Save this file as :code:`src/Article/Article.g`. Notice that the file name and path needs to match the defined namespace and entity name. Otherwise, the compiler will notify with an error that there is naming inconsistency.

To show a use cases of this entity lets create a new fresh controller for the purpose :code:`src/Article/ArticleController.g`::

    namespace Article

    rest controller /article {
        index {
            return new Article();
        }
    }

Compile the sources using :code:`$ glagol compile` and then :code:`$ curl localhost:8081/article`. The response should display the following json:

.. code-block:: json

    {
        "id": null,
        "title": null,
        "contents": null,
        "author": null
    }


