Installation
============
Lets create a simple Glagol DSL microservice. But first, lets setup Glagol DSL!

Prerequisites
-------------
Make sure you have java version 1.8 installed before you can use Glagol DSL.

Get the Glagol DSL server app
-----------------------------
Download the `glagol-dsl-server-XXX.jar` from the `latest release <https://github.com/BulgariaPHP/glagol-dsl/releases/latest>`_:

    $ wget https://github.com/BulgariaPHP/glagol-dsl/releases/download/0.5.0/glagol-dsl-server-0.5.0.jar ~/glagol-dsl-server.jar

Then you can alias the `glagol-server` as a command so that it can be used easily. To do that you simply add to the end of `~/.bashrc` (or `~/.zshrc`) the following command:

    alias glagol-server='java -Xmx400m -jar /full/path/tp/glagol-dsl-server.jar'

And then reload your rc:

    $ source ~/.bashrc

or for zsh:

    $ source ~/.zshrc

Get the Glagol DSL client app
-----------------------------
Similarly to the server app the client app jars are available on `Github releases <https://github.com/BulgariaPHP/glagol-dsl-client/releases/latest>`_. Download the latest release jar:

    $ wget https://github.com/BulgariaPHP/glagol-dsl-client/releases/download/0.3.6/glagol-dsl-client-0.3.6.jar ~/glagol-dsl-client.jar

Then you can alias the `glagol` as a command so that it can be used easily. To do that you simply add to the end of `~/.bashrc` (or `~/.zshrc`) the following command:

    alias glagol='java -jar /full/path/tp/glagol-dsl-client.jar'

And then reload your rc:

    $ source ~/.bashrc

or for zsh:

    $ source ~/.zshrc

Start the Glagol DSL server
---------------------------
Glagol DSL server app can be initiated using the following command:

    $ glagol-server daemon

The expected output is the following:

    Initializing Glagol...
    Daemon listens on port 51151...

The initialization phase might take up to 10 seconds depending on your machine's performance. You need to keep the Glagol DSL server running if you wish to compile.

