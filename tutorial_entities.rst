.. _tutorial_entities:

First Entity
============
One the most fundamental patterns of enterprise software nowadays is the Entity. In general, an entity is simply an object that is used to persist data and state during runtime. Moreover, entities are usually distinguished by some form of identity. For example, in the case with a relational database table an entity can be identified by a primary key field and its value.

Glagol DSL supports a built-in syntax declaration for entities that can be automatically bundled with database tables. Generally speaking, the purpose of having a built-in language structure for entities is to hide the underlying application logic from the developer and bring the focus on the entity as a business item itself.

Usually, entities are maintained with the help of the so called `Object-relational mapper` libraries. `Doctrine 2 ORM <doctrine-project.org>`_ is one of the popular ORMs in the PHP world. Therefore, it is being used by the Glagol DSL compiled codes. However, a Glagol DSL developer does not need to worry about ORMs because this part is taken care by the language and the compiler itself.

Create your first entity
------------------------
Lets create a Glagol DSL entity. Create a new package by making a directory named :code:`src/MusicLib`. Next, put the following code inside:

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        string genre;
        string author;
    }

Save this file as :code:`src/MusicLib/Song.g`. Notice that the file name and path needs to match the defined namespace and entity name. Otherwise, the compiler will notify with an error that there is naming inconsistency.

To show a use cases of this entity lets create a new fresh controller for the purpose :code:`src/MusicLib/SongController.g`:

.. code-block:: none

    namespace MusicLib

    rest controller /song {
        index {
            return new Song();
        }
    }

Compile the sources using :code:`$ glagol compile` and then :code:`$ curl localhost:8081/song`. The response should display the following json:

.. code-block:: json

    {
        "id": null,
        "title": null,
        "genre": null,
        "author": null
    }

Constructors
------------
Entities can have constructors. They are defined in a Java-like way:

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        string genre;
        string author;

        Song(string songTitle) {
            title = songTitle;
        }
    }

Constructors must use the name of the entity. Additionally, you can override constructors as long as they do not have duplicating signatures (the combination of arguments being passed):

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        string genre;
        string author;

        Song(string songTitle) {
            title = songTitle;
        }

        Song(string song, string author) {
            title = song;
            this.author = author;
        }
    }

Notice how the second constructor uses :code:`this` to assign property values. This is because the argument names match the property names. Unlike PHP, properties can be accessed directly from the scope of any method (including constructors). Only when an argument has the same name as a defined property you need to use :code:`this.propertyName` notation to differentiate the property from the parameter within the same scope.

Try to compile the app. What you will get is a type-check error like this:

.. code-block:: none

    Cannot compile, errors found:
    [/src/MusicLib/SongController.g:5] Cannot match constructor Song()

This is because you do not have a constructor that accepts no argument as used in the controller. Lets add some parameters:

.. code-block:: none

    namespace MusicLib

    rest controller /song {
        index {
            return new Song("Virus", "Marko Markovic Brass Band");
        }
    }

This instantiation will use the second constructor because of the matching signature (:code:`string, string`).
Note that glagol only accepts double quotes :code:`"` for strings and not single quotes.

Next, compile the app and after you :code:`$ curl localhost:8081/song` the output will be:

.. code-block:: json

    {
        "id": null,
        "title": "Virus",
        "genre": null,
        "author": "Marko Markovic Brass Band"
    }

Guards
------
Functional languages like Haskell support the concept of function *guards*. A guard is simply a boolean expression that is triggered before the function's body. Furthermore, if the guard expression evaluates as :code:`true` then the function logic will be executed. Otherwise, the next override declaration with the same signature will be checked until success. If no overriding qualifies for execution an error is thrown.

Glagol DSL implements guards that can be applied to constructors and methods. Lets modify our Song entity in a way it uses guards:

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        string genre;
        string author;

        Song(string title, string author) {
            this.title = title;
            this.author = author;
            this.genre = "Balkan";
        } when author == "Marko Markovic Brass Band";

        Song(string title, string author) {
            this.title = title;
            this.author = author;
            this.genre = "Jazz";
        } when author == "Miles Davis";
    }

Obviously, if you instantiate the entity as :code:`new Song("Virus", "Marko Markovic Brass Band")` the genre will be set to 'Balkan'. The second constructor has a guard that will evaluate as true if the artist is 'Miles Davis', and as a result it will set the genre to 'Jazz'.

In general, you can think of guards as a way to structurally avoid if-else statements. Their only purpose in Glagol DSL is to reduce code volume and enhance readability. Otherwise, guards work is any other typical conditional structure.

You might have noticed something in the example above, and that is that there is no *fallback* constructor yet - a one with the same signature but with no guard. In a case where we instantiate a :code:`Song` entity and the author is neither :code:`Marko Markovic Brass Band` or :code:`Miles Davis` what would be the outcome? Logically - it should fail with an error, and this is exactly the way Glagol DSL will behave too. Lets update the controller with the following code:

.. code-block:: none

    namespace MusicLib

    rest controller /song {
        index {
            return new Song("Soul Power 74", "Maceo Parker");
        }
    }

Since there is not constructor to handle the entity instantiation the app will respond with an error message like this:

.. code-block:: json

    {
        "message": "Sorry, something went wrong",
        "error": "Cannot match constructor for Song",
        "trace": [
            {
                "file": "src/MusicLib/SongController.g",
                "line": 5,
                "name": null
            }
        ]
    }

Glagol DSL will try to match methods/constructors with guards first. Furthermore, you can see that errors that originally happening in the generated PHP are mapped to the actual :code:`*.g` source files.

Lets default the song genre by adding a constructor without a guard:

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        string genre;
        string author;

        Song(string title, string author) {
            this.title = title;
            this.author = author;
            this.genre = "Balkan";
        } when author == "Marko Markovic Brass Band";

        Song(string title, string author) {
            this.title = title;
            this.author = author;
            this.genre = "Jazz";
        } when author == "Miles Davis";

        Song(string title, string author) {
            this.title = title;
            this.author = author;
            this.genre = "Unknown";
        }
    }

The next chapter explains how to read, persist and delete entities from the database.

Relationships
------
Glagol DSL also supports database relationships. Say we have a :code:`genres` table that the :code:`songs` table has a foreign key relation to from a field :code:`genre_id`.

.. code-block:: console

    mysql> SELECT * FROM glagol.songs;
    +----+-------+----------+---------------------------+
    | id | title | genre_id | author                    |
    +----+-------+----------+---------------------------+
    |  1 | Virus |        1 | Marko Markovic Brass Band |
    +----+-------+----------+---------------------------+

	mysql> SELECT * FROM glagol.genres;
    +----+--------+
	| id | name   |
    +----+--------+
	|  1 | Balkan |
    +----+--------+

If you create a Genre entity, you can type hint it in the Song entity and Glagol DSL will automatically lazy load it.

.. code-block:: none

	namespace MusicLib

	@table="genres"
	entity Genre {
		@id
		@sequence
		int id;

		string name;
	}

.. code-block:: none

    namespace MusicLib

    entity Song {
        int id;
        string title;
        Genre genre;
        string author;
	}

Querying songs will now result in

.. code-block:: json

    {
        "id": null,
        "title": "Virus",
        "genre": {
            "id": 1,
            "name": "Balkan"
        },
        "author": "Marko Markovic Brass Band"
    }
