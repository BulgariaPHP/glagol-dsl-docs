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
      `name` VARCHAR(45) NULL,
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

