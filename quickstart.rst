Quickstart
==========

.. _quickstart_docker:

New project (with Docker)
-------------------------

After a successful :ref:`installation` of Glagol DSL server and client apps you are ready to start developing. As discussed within the installation section the fastest way to start developing is to use Docker and Docker Compose.

Create a new project structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
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


Next you can proceed directly to :ref:`hello_world_controller`.

.. _quickstart_no_docker:

New project (without Docker)
----------------------------
This section describes how you can manually setup your environment for developing with Glagol DSL.

Install PHP, MySQL and a Web Server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You need PHP 7.1 and MySQL 5.7 at the very least. The choice of a web server is not crucial, both nginx and apache were tested and proven to work perfectly fine.

PHP DS extension
################
Glagol DSL data structures rely on the `ds <https://php.net/manual/en/ds.installation.php>`_ extension. The simplest way to install is using pecl::

    $ sudo pecl install ds
    $ sudo phpenmod ds

PHP Pdo extensions
##################
Currently, Glagol DSL supports only the MySQL database as a possible ORM backend. Therefore, the :code:`pdo_mysql` extension is required. On Ubuntu/Debian you can install the MySQL PDO driver by typing::

    $ sudo apt-get install php-mysql

Alternatively, you can also install it using pecl::

    $ sudo pecl install pdo pdo_mysql
    $ sudo phpenmod pdo pdo_mysql

PHP mbstring extension
######################
Lumen framework requires the mbstring PHP extension. On Ubuntu you can install it simply by typing::

    $ sudo apt-get install php-mbstring

Or alternatively using pecl::

    $ sudo pecl install mbstring
    $ sudo phpenmod mbstring

Composer
########
Finally, you need to make sure that you have composer installed. On Ubuntu you can install it using the package manager::

    $ sudo apt-get install composer

.. hint::

    If you want to significantly speed-up your composer you might consider installing the global performance boost package `hirak/prestissimo <https://github.com/hirak/prestissimo>`_: :code:`composer global require hirak/prestissimo`


Create a new project structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Lets create a new project. For the purpose we need a new directory::

    $ mkdir glagol-hello-world

Navigate inside it and create two sub-directories :code:`src` and :code:`out`::

    $ cd glagol-hello-world
    $ mkdir src
    $ mkdir out

The :code:`src` folder will host the Glagol :code:`*.g` source files, and the :code:`out` directory will be used for the compiled sources.

.. attention::

    You need to have the Glagol DSL Server app running before you proceed. Remember, you can start the server app by simply running :code:`$ glagol-server daemon`.

Next, create a new package by making a new directory within the :code:`src` folder:;

    $ mkdir src/HelloWorld

At the end your directory structure should look like this::

    .
    ├── out
    └── src
        └── HelloWorld

.. important::

    Make sure that your Web Server's virtual host maps to :code:`out/public` folder. This is where the :code:`index.php` is landing and where the routing starts.

Next, you need to compile for first time! Simply run the compile command::

    $ glagol compile

The output should be something like::

    Successfully compiled glagol project


Install Composer dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The final step is to install the PHP Composer packages::

    $ composer install --prefer-dist -d out/

.. hint::

    PHP compiled sources are generated in the :code:`./out/` directory of your project's root. The nginx server root is configured to map to :code:`./out/public` directory (*This is* :code:`/var/www/html/public` *from within the nginx docker container*).

.. _hello_world_controller:

Hello world controller
----------------------
After you have your directory structure set up it is time to create your first endpoint - the index page. For the purpose create a file :code:`src/HelloWorld/IndexController.g` with the following contents::

    namespace HelloWorld

    rest controller /hello-world {
        index {
            return "Hello world!";
        }
    }

The snippet above represents an HTTP controller that is set to handle the index page of the :code:`/hello-world` route by responding with a :code:`Hello world!` message.

The :code:`index` declaration is in fact the action method that is handling the GET request.

Furthermore, you might have noticed that the controller declaration does not have a name but only a route. Although controllers in Glagol DSL do look like classes they are a special type of structure. For example, controllers cannot be instantiated and they carry the routing information with them. More about controllers can be found in the language reference guide.

Compile the sources
^^^^^^^^^^^^^^^^^^^
Now compile the project! Simply run the compile command from your project's root directory::

    $ glagol compile

The output should be something like::

    Successfully compiled glagol project


.. hint::

    PHP compiled sources are generated in the :code:`./out/` directory of your project's root. The nginx server root is configured to map to :code:`./out/public` directory (*This is* :code:`/var/www/html/public` *from within the nginx docker container*).

Testing the new app
^^^^^^^^^^^^^^^^^^^
The Docker Compose default project skeleton will run nginx on port :code:`8081` (if you installed a fresh nginx manually the port is porbably :code:`80`)::

    $ curl localhost:8081/hello-world

The microservice should respond with::

    $ curl localhost:8081/hello-world
    Hello world!
