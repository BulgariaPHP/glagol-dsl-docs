.. _setup_no_docker:

Setup new project (without Docker)
==================================
This section describes how you can manually setup your environment for developing with Glagol DSL.

Install PHP, MySQL and a Web Server
-----------------------------------
You need PHP 7.1 and MySQL 5.7 at the very least. The choice of a web server is not crucial, both nginx and apache were tested and proven to work perfectly fine.

PHP DS extension
^^^^^^^^^^^^^^^^
Glagol DSL data structures rely on the `ds <https://php.net/manual/en/ds.installation.php>`_ extension. The simplest way to install is using pecl::

    $ sudo pecl install ds
    $ sudo phpenmod ds

PHP Pdo extensions
^^^^^^^^^^^^^^^^^^
Currently, Glagol DSL supports only the MySQL database as a possible ORM backend. Therefore, the :code:`pdo_mysql` extension is required. On Ubuntu/Debian you can install the MySQL PDO driver by typing::

    $ sudo apt-get install php-mysql

Alternatively, you can also install it using pecl::

    $ sudo pecl install pdo pdo_mysql
    $ sudo phpenmod pdo pdo_mysql

PHP mbstring extension
^^^^^^^^^^^^^^^^^^^^^^
Lumen framework requires the mbstring PHP extension. On Ubuntu you can install it simply by typing::

    $ sudo apt-get install php-mbstring

Or alternatively using pecl::

    $ sudo pecl install mbstring
    $ sudo phpenmod mbstring

Composer
^^^^^^^^
Finally, you need to make sure that you have composer installed. On Ubuntu you can install it using the package manager::

    $ sudo apt-get install composer

.. hint::

    If you want to significantly speed-up your composer you might consider installing the global performance boost package `hirak/prestissimo <https://github.com/hirak/prestissimo>`_: :code:`composer global require hirak/prestissimo`


Create a new project structure
------------------------------
Lets create a new project. For the purpose we need a new directory::

    $ mkdir glagol-hello-world

Navigate inside it and create two sub-directories :code:`src` and :code:`out`::

    $ cd glagol-hello-world
    $ mkdir src
    $ mkdir out

The :code:`src` folder will host the Glagol :code:`*.g` source files, and the :code:`out` directory will be used for the compiled sources.

.. attention::

    You need to have the Glagol DSL Server app running before you proceed. Remember, you can start the server app by simply running :code:`$ glagol-server daemon`.

Next, create a new package by making a new directory within the :code:`src` folder::

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
-----------------------------
The final step is to install the PHP Composer packages::

    $ composer install --prefer-dist -d out/

.. hint::

    PHP compiled sources are generated in the :code:`./out/` directory of your project's root. Make sure to map your web server's root to :code:`./out/public` directory.
