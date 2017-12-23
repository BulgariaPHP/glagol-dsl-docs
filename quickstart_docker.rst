.. _quickstart_docker:

Quickstart with Docker Compose
==============================
After a successful :ref:`installation` of Glagol DSL server and client apps you are ready to start developing. As discussed within the installation section the fastest way to start developing is to use Docker and Docker Compose.

Create a new project structure
------------------------------
Simply clone the default project skeleton::

    $ mkdir glagol-hello-world
    $ git clone git@github.com:glagol-dsl/docker-compose-project-skeleton.git glagol-hello-world

.. attention::

    You need to have the Glagol DSL Server app running before you proceed. Remember, you can start the server app by simply running :code:`$ glagol-server daemon`.

After you clone the default project skeleton you can compile it for first time! Simply run the compile command::

    $ glagol compile

The output should be something like::

    Successfully compiled glagol project

And then you are ready to spin-up the Docker Compose environment::

    $ docker-compose up

.. hint::

    The Docker Compose configuration will automatically install all PHP Composer dependencies upon containers startup. In fact, it will do so every time you spin-up the containers. See :code:`./scripts/composer.sh` for how it works under the hood.

Testing the new app
-------------------
After you have the Docker Compose containers up and running you can simply :code:`curl` the index page. The default project skeleton will run nginx on port `8081`::

    $ curl localhost:8081/

The microservice should respond with::

    $ curl localhost:8081/
    Hello world!

.. hint::

    PHP compiled sources are generated in the :code:`./out/` directory of your project's root. The nginx server root is configured to map to :code:`./out/public` directory (*This is* :code:`/var/www/html/public` *from within the nginx docker container*).
