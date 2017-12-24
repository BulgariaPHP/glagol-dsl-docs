.. _tutorial_entities:

First Entity
============
One the most fundamental patterns of enterprise software nowadays is the Entity. Entity is simply an object that is used to persist data and state during runtime. Furthermore, entities are usually distinguished by some form of identity. For example, in the case with a relational database table, an entity can be identified by a primary key field and its value.

Glagol DSL supports a built-in syntax declaration for entities that can be automatically bundled with database tables. Generally speaking, the purpose of having a built-in language structure for entities is to hide the underlying application logic from the developer and bring the focus on the entity as a business item itself.

Usually, entities are maintained with the help of the so called `Object-relational mapper` libraries. `Doctrine 2 ORM <doctrine-project.org>`_ is one of the popular ORMs in the PHP world. Therefore, it is being used by the Glagol DSL compiled codes. However, a Glagol DSL developer does not need to worry about ORMs because this part is taken care by the language and the compiler itself.

Create your first entity
------------------------
Lets create a Glagol DSL entity. Create a new package by making a directory named :code:`src/Article`. Next, put the following code inside:

.. code-block:: none

    namespace Article

    entity Article {
        int id;
        string title;
        string category;
        string author;
    }

Save this file as :code:`src/Article/Article.g`. Notice that the file name and path needs to match the defined namespace and entity name. Otherwise, the compiler will notify with an error that there is naming inconsistency.

To show a use cases of this entity lets create a new fresh controller for the purpose :code:`src/Article/ArticleController.g`:

.. code-block:: none

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
        "category": null,
        "author": null
    }

Constructors
------------
Entities can have constructors. They are defined in a Java-like way:

.. code-block:: none

    namespace Article

    entity Article {
        int id;
        string title;
        string category;
        string author;

        Article(string articleTitle) {
            title = articleTitle;
        }
    }

Constructors must use the name of the entity. Furthermore, you can override constructors as long as they do not have duplicating signatures (the combination of arguments being passed):

.. code-block:: none

    namespace Article

    entity Article {
        int id;
        string title;
        string category;
        string author;

        Article(string articleTitle) {
            title = articleTitle;
        }

        Article(string article, string author) {
            this.title = article;
            this.author = author;
        }
    }

Notice how the second constructor uses :code:`this` to assign property values. This is because the argument names match the property names. Unlike PHP, properties can be accessed directly from the scope of any method (including constructors). Only when an argument has the same name as a defined property you need to use :code:`this.propertyName` notation to differentiate the property from the parameter within the same scope.

Furthermore, if you now try to compile the app you will most probably get a type-check error like this:

.. code-block:: none

    Cannot compile, errors found:
    [/src/Article/ArticleController.g:5] Cannot match constructor Article()

This is because you do not have a constructor that accepts no argument as used in the controller. Lets add some parameters:

.. code-block:: none

    namespace Article

    rest controller /article {
        index {
            return new Article("Virus", "Marko Markovic Brass Band");
        }
    }

This instantiation will use the second constructor because of the mathcing signature (:code:`string, string`).
Next, compile the app and after you :code:`$ curl localhost:8081/article` the output will be:

.. code-block:: json

    {
        "id": null,
        "title": "Virus",
        "category": null,
        "author": "Marko Markovic Brass Band"
    }

Guards
------
Functional languages like Haskell support the concept of function *guards*. A guard is simply a boolean expression that is triggered before the function's body. Furthermore, if the guard expression evaluates as :code:`true` then the function logic will be executed. Otherwise, the next override declaration with the same signature will be checked until success. If no overriding qualifies for execution an error is thrown.

Glagol DSL implements guards that can be applied to constructors and methods.