.. _tutorial_rest:

Rest controllers
================
The examples so far always relied on modifications of the index page of the Song controller. Although it is easy for displaying tutorial examples this approach is highly inappropriate for real-world scenarios. Luckily, Glagol DSL provides a built-in Rest API support in controllers.

Index action
------------
Every rest controller supports the standard Rest actions which are based on the well-known HTTP 1.1 request methods. As you might have noticed already, so far we used the :code:`index` action for testing. Generally speaking, when we want to run the index we simply make a :code:`GET` request to the route that the controller defines. The response can be any value. Usually, the Rest index will return a list of resources. In our case, this fits with the result of querying for all entities. Therefore, lets modify our Songs index to select all song entities:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }
    }

We already had this exact bit in the previous tutorial. Lets keep it this way!

Show action
-----------
What if we want to request a single resource? For this task Glagol DSL provides the developers with a :code:`show` action. Lets introduce it:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (int id) {
            return songs.find(id);
        }
    }

The main difference is that the show action accepts an argument which is used as a criteria for finding a resource. In the example above we rely on the existing :code:`find` repository method that will do the job.

Now compile the sources and call the following curl command:

.. code-block:: console

    $ curl localhost:8081/song/1

Naturally, the :code:`show` action is initiated by a :code:`GET` request method in combination with an argument appended to the end of the route. Therefore, a :code:`show` request URI has the :code:`/song/{parameter}` format.

However, there is an alternative way of finding entities when we rely on the primary key field that is annotated with :code:`@id`. For the purpose, we can even avoid our manually created :code:`find` method or the repository altogether. To put it simply, developers can utilize the :code:`@autofind` annotation to the first parameter of the action like this:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (@autofind Song song) {
            return song;
        }
    }

Lets look at the differences. Instead of accepting :code:`int id` as a parameter, we type :code:`Song song` preceded by the :code:`@autofind` annotation. This very annotation tells Glagol DSL to attempt to find the entity by primary key field and the value of the parameter being passed with the request.

.. important::

    The annotation approach does not require you to have a :code:`find` repository method whatsoever. It is a built-in behavior provided the Glagol DSL runtime environment.

Store action
------------
The :code:`store` method is there to handle :code:`POST` requests aimed to create new resources. Additionally, the :code:`Glagol::Http::Request` object is necessary for extracting input :code:`POST` data from the request:

.. code-block:: none

    namespace MusicLib

    import Glagol::Http::Request;

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (@autofind Song song) {
            return song;
        }

        store (Request request) {
            Song song = new Song(request.input("title"), request.input("author", "unknown"));

            songs.save(song);
            return song;
        }
    }

Do not forget to compile the source!

.. hint::

    The :code:`Glagol::Http::Request` object is provided by the Glagol DSL standard library. It provides the following methods for extracting :code:`POST` data::

        string input(string key);
        string input(string key, string default);

    Use the :code:`string default` parameter to provide a default value if the :code:`string key` does not exist in the request payload.

Since Glagol DSL relies on Lumen Framework for its runtime both json and x-www-form-urlencoded payloads are supported. For this example we are going to use a json payload:

.. code-block:: console

    $ curl -X POST -d '{"title":"Born to raise hell", "author": "Motorhead"}' -H "Content-Type: application/json" localhost:8081/song

Update action
-------------
The :code:`update` action is similar to both show and store actions in a way. First, just like :code:`show` it requires a parameter by which to identify a resource. Secondly, just like :code:`store` it can use the :code:`input()` methods from :code:`Glagol::Http::Request`.

In contrast to both actions, the HTTP request method for :code:`update` is :code:`PUT`. Lets look at this example:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (@autofind Song song) {
            return song;
        }

        store (Request request) {
            Song song = new Song(request.input("title"), request.input("author", "unknown"));

            songs.save(song);
            return song;
        }

        update (Request request, @autofind Song song) {
            song.setTitle(request.input("title"));
            song.setAuthor(request.input("author", "unknown"));

            songs.save(song);

            return song;
        }
    }

Additionally, the example above requires two setter methods in the entity:

.. code-block:: none

    namespace MusicLib

    @table="songs"
    entity Song {
        // ... properties and constructors from before...

        public void setTitle(string title) {
            this.title = title;
        }

        public void setAuthor(string author) {
            this.author = author;
        }
    }

Compile the sources and test with curl:

.. code-block:: console

    $ curl -X PUT -d '{"title":"Killed by death", "author": "Motorhead"}' -H "Content-Type: application/json" localhost:8081/song/2

Delete action
-------------
Glagol DSL provides the :code:`delete` action to handle :code:`DELETE` HTTP requests:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (@autofind Song song) {
            return song;
        }

        store (Request request) {
            Song song = new Song(request.input("title"), request.input("author", "unknown"));

            songs.save(song);
            return song;
        }

        update (Request request, @autofind Song song) {
            song.setTitle(request.input("title"));
            song.setAuthor(request.input("author", "unknown"));

            songs.save(song);

            return song;
        }

        delete (@autofind Song song) {
            songs.remove(song);
        }
    }

Compile the sources and test with curl:

.. code-block:: console

    $ curl -X DELETE localhost:8081/song/1

Create and edit actions
-----------------------
Additionally, you can also implement the :code:`create` and :code:`edit` actions that are basically responding to :code:`GET` requests but with additional route extensions.

First, the :code:`create` action is usually used to return an initial state entity. Typically this functionality is used to retrieve a blank resource from the service:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get selfie;

        index {
            return songs.findAll();
        }

        show (@autofind Song song) {
            return song;
        }

        store (Request request) {
            Song song = new Song(request.input("title"), request.input("author", "unknown"));

            songs.save(song);
            return song;
        }

        update (Request request, @autofind Song song) {
            song.setTitle(request.input("title"));
            song.setAuthor(request.input("author", "unknown"));

            songs.save(song);

            return song;
        }

        delete (@autofind Song song) {
            songs.remove(song);
        }

        create {
            return new Song("Please, enter song title", "Please, enter song author (band)");
        }

        edit (@autofind Song song) {
            return song;
        }
    }

To get the response from this endpoint you need to append :code:`/create` to the route like this:

.. code-block:: console

    $ curl localhost:8081/song/create

Similarly, :code:`edit` is just like :code:`show` but it requires the :code:`/edit` extension to be accessed:

.. code-block:: console

    $ curl localhost:8081/song/1/edit
