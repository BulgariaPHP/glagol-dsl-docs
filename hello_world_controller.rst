.. _hello_world_controller:

Hello world controller
======================
After you have your directory structure set up it is time to create your first endpoint - the hello world page. For the purpose create a file :code:`src/HelloWorld/HelloWorldController.g` with the following contents::

    namespace HelloWorld

    rest controller /hello-world {
        index {
            return "Hello world!";
        }
    }

The snippet above represents an HTTP controller that is set to handle the index page of the :code:`/hello-world` route by responding with a :code:`Hello world!` message.

The :code:`index` declaration is in fact the action method that is handling the GET request.

Furthermore, you might have noticed that the controller declaration does not have a name but only a route. Although controllers in Glagol DSL do look like classes they are a special type of structure. For example, controllers cannot be instantiated by the developer and they carry the routing information with them. More about controllers can be found in the language reference guide.

Compile the sources
-------------------
Now compile the project! Simply run the compile command from your project's root directory::

    $ glagol compile

The output should be something like::

    Successfully compiled glagol project


.. hint::

    PHP compiled sources are generated in the :code:`./out/` directory of your project's root. If you are not using the preconfigured Docker Compose project skeleton you need to map your web server's root to :code:`./out/public` directory.

Testing the new app
-------------------
The Docker Compose default project skeleton will run nginx on port :code:`8081` (if you installed a fresh nginx manually the port is porbably :code:`80`)::

    $ curl localhost:8081/hello-world

The microservice should respond with::

    $ curl localhost:8081/hello-world
    Hello world!
