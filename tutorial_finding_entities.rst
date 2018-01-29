.. _tutorial_finding_entities:

Querying
========
So far we learnt how to create and persist entities but what about querying for such? Long story short, Glagol DSL uses a built-in SQL dialect for the purpose. In general, there are a few things to consider about the Glagol Query Language:

* Queries can only be utilized within repository methods
* Queries target **entities** not database tables
* Queries are executed immediately
* Queries are a type of expressions

Finding all entities
--------------------
We start by creating a :code:`findAll` method in the Song repository:

.. code-block:: none

    namespace MusicLib

    repository for Song {
        public void save(Song song) {
            persist song;
            flush;
        }

        public Song[] findAll() {
            User[] users = SELECT u[] FROM User u;

            return users;
        }
    }

Secondly, lets look closely at what we did here. Obviously, the :code:`SELECT u FROM User u` is a Glagol Query. One can easily recognize the great similarities between SQL and Glagol QL. However, think of it this way - Glagol Query Language is used to ask for entities where SQL usually queries database tables directly. Therefore, the result of a Glagol Query is either an entity or a collection of entities.

Lets break down the query from above. The first part is :code:`SELECT u[]`. Simply put, we say that we want to select a list of :code:`u`'s where :code:`u` is just an alias defined in the :code:`FROM` part. Furthermore, :code:`FROM User u` is exactly the part where we first select the target entity and then we alias it so it can be referenced in other parts of the query.

Notice how the selection part says :code:`u[]`. In case you remove the brackets then the query will return the first entity directly. Hence, when you want to select the whole result set then you simply add the brackets.

Thirdly, since queries are just expressions we can refactor the bit from above and simply return the query expression directly:

.. code-block:: none

    namespace MusicLib

    repository for Song {
        public void save(Song song) {
            persist song;
            flush;
        }

        public Song[] findAll() {
            return SELECT u[] FROM User u;
        }
    }

Finally, in order to see how the query works we simply use it in the controller:


.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }
    }

Last thing to do is to compile using :code:`$ glagol compile`. Now simply :code:`curl localhost:8081/song` and you will see a list of song objects!

Querying for a single entity
----------------------------
Next task is to create a typical :code:`find(int id)` method that will query for just one entity. You guessed it - we need a :code:`WHERE` clause:

.. code-block:: none

    namespace MusicLib

    repository for Song {
        public void save(Song song) {
            persist song;
            flush;
        }

        public Song[] findAll() {
            return SELECT u[] FROM User u;
        }

        public Song find(int id) {
            return SELECT u FROM User u WHERE u.id = <<id>>;
        }
    }

As mentioned earlier, if we want to select only one entity we simply write :code:`SELECT u`. Secondly, lets investigate the :code:`WHERE u.id = <<id>>` clause. It is obvious that we access a field from the entity by targeting the :code:`u` alias. The right side of the equation indicates that we embed a Glagol DSL expression. In the example above we simply pass the :code:`id` parameter that is provided by the method. However, we can use any expression we like there (like :code:`WHERE u.id = <<id + 232>>`). The :code:`<<` and :code:`>>` are just delimiters that indicate the start and end of an embedded expression.

In order to show how the finder method works lets modify our sandbox controller in the following way:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.find(1);
        }
    }

Compile the source and simply :code:`curl localhost:8081/song` to test as usual.

In the next chapter we are going to learn how to put everything together under a unified Rest API using the controller.
