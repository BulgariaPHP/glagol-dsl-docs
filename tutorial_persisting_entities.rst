.. _tutorial_persisting_entities:

Repositories and entities
=========================
In the previous chapter we investigated some basics of creating and using entities. However, the examples so far were leaving the entities static - without persistence with a database.

Prepare the database
--------------------
Lets create a database table for our :code:`MusicLib::Song` entity:

.. code-block:: sql

    USE `glagol`;

    CREATE TABLE `songs` (
      `id` INT NOT NULL AUTO_INCREMENT,
      `title` VARCHAR(45) NULL,
      `genre` VARCHAR(45) NULL,
      `author` VARCHAR(45) NULL,
      PRIMARY KEY (`id`));

.. attention::

    If you are using the Docker Compose default project structure you can just put the SQL snippet displayed above in the :code:`./database/data.sql` file. The database folder is imported by the MySQL docker container upon build - just rebuild your stack and the table will be created and ready to use. Additionally, you can tweak the database name the app is using by changing the :code:`DB_DATABASE` environmental variable from :code:`docker-compose.yml`. The database name for any Glagol DSL app defaults to :code:`glagol`.

Annotating the entity
---------------------
Next step is to tell Glagol DSL that the :code:`Song` entity related to the :code:`glagol.songs` database table.

First, add the :code:`@table` annotation just before the declaration of the entity:

.. code-block:: none

    namespace MusicLib

    @table="songs"
    entity Song {
        int id;
        string title;
        string genre;
        string author;
    }

After you compile the sources the environment will know that the :code:`Song` entity is bundled with the :code:`glagol.songs` table.

Secondly, we need to annotate the primary key field and flag it as auto-incremented. Simply add the :code:`@id` and :code:`@sequence` annotations just before the :code:`int id;` property declaration:

.. code-block:: none

    namespace MusicLib

    @table="songs"
    entity Song {
        @id
        @sequence
        int id;
        string title;
        string genre;
        string author;
    }

The :code:`@id` annotation will tell Glagol DSL that the targeted field is the primary key field. As for the :code:`@sequence` annotation - it will indicate that the field is set for auto-increment.

Compile the sources using the already familiar :code:`glagol compile` command.

Now we told Glagol DSL about all the metadata required to bundle the entity with an existing database table.

Songs repository
----------------
Lets insert some data into the database using the Song entity!

First, we need a Repository object. In general, repositories are a special type of objects that hold the sole responsibility of supporting the lifecycle of entities. Moreover, a `Repository <http://deviq.com/repository-pattern/>`_ is a frequently used enterprise design pattern which has been repeatedly publicised by a number of influential authors such as `Martin Fowler <https://martinfowler.com/eaaCatalog/repository.html>`_ and `Eric Evans <http://dddcommunity.org/uncategorized/toc/>`_ throughout the last two decades. To put it simply, a repository can persist(create, update), remove and retrieve entities from the data storage.

Glagol DSL provides a built-in syntax declaration for repositories:

.. code-block:: none

    namespace MusicLib

    repository for Song {

    }

Save this code as :code:`src/MusicLib/SongRepository.g`.

Lets investigate the code snippet. From :code:`repository for Song` we can conclude that repositories are, in a way, *attached* to entities. Additionally, Glagol DSL allows only one repository per entity (one-on-one relationship) to be created - doing otherwise will result in typecheck errors during compilation. Last but not least, the source file name has to follow the mandatory naming convention of :code:`<Entity>Repository.g` where :code:`<Entity>` is the name of the targeted entity.

So far so good. However, our new repository does not do anything yet. Lets change that by introducing a :code:`save` method:

.. code-block:: none

    namespace MusicLib

    repository for Song {
        public void save(Song song) {
            persist song;
            flush;
        }
    }

Yep - it is *that* simple. Repositories expose the :code:`persist` and :code:`flush` statements. The first one tells Glagol DSL that we want to initiate the persistence of the object that is being passed. Secondly, :code:`flush` statement will save all the data into the data storage. Think of it this way - :code:`persist` will tell Glagol DSL *"Hey, here is an object for you to keep an eye on!"*, and :code:`flush` says *"Bring my changes to the database, please!"*.

.. note::

    Entity persisting and flushing is inspired and based on `Doctrine 2 ORM
    <https://www.doctrine-project.org/projects/doctrine-orm/en/latest/reference/working-with-objects.html>`_'s way of operating with entities.

Finally, lets save our Song entity into the database! Modify the :code:`SongController.g` to use the repository:

.. code-block:: none

    namespace MusicLib

    rest controller /song {

        repository<Song> songs = get repository<Song>;

        index {
            Song balkanSong = new Song("Virus", "Marko Markovic Brass Band");

            songs.save(balkanSong);

            return balkanSong;
        }
    }

Lets investigate this piece of code. The very first thing to notice is :code:`repository<Song> songs = get repository<Song>;` - in this line we simply define a new property :code:`songs` which is of type Song repository type. Moreover, you lets focus on two main keypoints here:

- :code:`repository<Song>` - this is the syntax used to address repository types;
- :code:`get repository<Song>` - this indicates that the property should be automatically set with the repository instance for a value.

.. hint::

    The line :code:`repository<Song> songs = get repository<Song>;` can be simplified to :code:`repository<Song> songs = get selfie;`. In general, the **selfie** keyword will reflect the type declared in the property (in this case :code:`repository<Song>`).

Now just compile once again and test using :code:`curl localhost:8081/song`! You will see a response just similar to:

.. code-block:: json

    {
        "id": 1,
        "title": "Virus",
        "genre": "Balkan",
        "author": "Marko Markovic Brass Band"
    }

Notice that this time there is a value assigned to the :code:`id` field. Generally speaking, this is the first indicator that the data was successfully inserted into the data storage. In fact, lets query MySQL directly to see the stored data:

.. code-block:: console

    mysql> SELECT * FROM glagol.songs;
    +----+-------+--------+---------------------------+
    | id | title | genre  | author                    |
    +----+-------+--------+---------------------------+
    |  1 | Virus | Balkan | Marko Markovic Brass Band |
    +----+-------+--------+---------------------------+
    1 row in set (0.00 sec)

Removing entities
-----------------
Very similarly to persisting entities we can also remove such. However, instead of the :code:`persist` statement Glagol DSL provides the :code:`remove` counterpart:

.. code-block:: none

    namespace MusicLib

    repository for Song {
        public void save(Song song) {
            persist song;
            flush;
        }

        public void remove(Song song) {
            remove song;
            flush;
        }
    }

Notice, that flushing the requested changes is still necessary if you want to delete the entity records.

In the next chapter we are going to look into how to query entities using Glagol DSL's embedded query language!
