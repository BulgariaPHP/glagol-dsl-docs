.. _installation:

Installation
============
Before we create a simple Glagol DSL microservice lets setup Glagol DSL!

Prerequisites
-------------

Java
####
Make sure you have java version 1.8 installed before you can use Glagol DSL::

    $ java -version
    java version "1.8.0_151"
    Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
    Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)

Environment
###########
Running Glagol DSL apps requires a very typical PHP-MySQL-WebServer bundle. However, if you use Docker and Docker Compose, you might just grab `the preconfigured Docker Compose default project skeleton for Glagol DSL apps <https://github.com/glagol-dsl/docker-compose-project-skeleton>`_. This repository contains a preconfigured development-ready :code:`docker-compose.yml` for Glagol DSL applications. **See** :ref:`quickstart_docker` **tutorial for how to use the default project skeleton.**

.. hint::

    Learn how to install Docker from here: https://docs.docker.com/engine/installation/ and Docker Compose from here: https://docs.docker.com/compose/install/ .

If for any reason you wish to manually configure the environment here is a list of dependencies that Glagol DSL requires:

- PHP>=7.1
- MySQL>=5.7
- WebServer (nginx, apache, etc)
- :code:`pdo` and :code:`pdo_mysql` PHP extensions
- :code:`ds` PHP extension (:code:`pecl install ds`)
- :code:`mbstring` PHP extension (:code:`pecl install mbstring`)

Get the server app
------------------
Download the :code:`glagol-dsl-server-XXX.jar` from the `latest release <https://github.com/BulgariaPHP/glagol-dsl/releases/latest>`_::

    $ wget https://github.com/BulgariaPHP/glagol-dsl/releases/download/0.5.0/glagol-dsl-server-0.5.0.jar ~/glagol-dsl-server.jar

Then you can alias the :code:`glagol-server` as a command so that it can be used easily. To do that you simply add to the end of :code:`~/.bashrc` (or :code:`~/.zshrc`) the following command::

    alias glagol-server='java -Xmx400m -jar /full/path/tp/glagol-dsl-server.jar'

And then reload your rc::

    $ source ~/.bashrc

or for zsh::

    $ source ~/.zshrc

Get the client app
-----------------------------
Similarly to the server app the client app jars are available on `Github releases <https://github.com/BulgariaPHP/glagol-dsl-client/releases/latest>`_. Download the latest release jar::

    $ wget https://github.com/BulgariaPHP/glagol-dsl-client/releases/download/0.3.6/glagol-dsl-client-0.3.6.jar ~/glagol-dsl-client.jar

Then you can alias the :code:`glagol` as a command so that it can be used easily. To do that you simply add to the end of :code:`~/.bashrc` (or :code:`~/.zshrc`) the following command::

    alias glagol='java -jar /full/path/tp/glagol-dsl-client.jar'

And then reload your rc::

    $ source ~/.bashrc

or for zsh::

    $ source ~/.zshrc

Start the Glagol DSL server
---------------------------
Glagol DSL server app can be initiated using the following command::

    $ glagol-server daemon

The expected output is the following::

    Initializing Glagol...
    Daemon listens on port 51151...

The initialization phase might take up to 10 seconds depending on your machine's performance. You need to keep the Glagol DSL server running if you wish to compile.

Next section will explain more about setting up an initial project structure, compiling and running an app.
