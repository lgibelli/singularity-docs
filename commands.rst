========
Commands
========

-------------
Command Usage
-------------

.. _sec:commandlineinterface:

The Singularity command
=======================

| Singularity uses a primary command wrapper called ``singularity``. When you run ``singularity``
  without any options or arguments it will dump the high level usage
  syntax.
| The general usage form is:

::

    $ singularity (opts1) [subcommand] (opts2) ...

| If you type singularity without any arguments, you will see a high
  level help for all arguments. The main options include:
| **Container Actions**

-  `build <#id55>`_ : Build a container on your user endpoint or build environment

-  `exec <#id60>`_ : Execute a command to your container

-  `inspect <#id62>`_ : See labels, run and test scripts, and environment variables

-  `pull <#id63>`_ : pull an image from Docker or Singularity Hub

-  `run <#id67>`_ : Run your image as an executable

-  `shell <#id72>`_ : Shell into your image

| **Image Commands**

-  `image.import <#id75>`_ : import layers or other file content to your image

-  `image.export <#id73>`_ : export the contents of the image to tar or stream

-  `image.create <#id76>`_ : create a new image, using the old ext3 filesystem

-  `image.expand <#id74>`_ : increase the size of your image (old ext3)

| **Instance Commands**
| Instances were added in 2.4. This list is brief, and likely to expand
  with further development.

-  `instances <#why-container-instances>`_ : Start, stop, and list container instances

| **Deprecated Commands**
| The following commands are deprecated in 2.4 and will be removed in
  future releases.

-  `bootstrap <#id90>`_ : Bootstrap a container recipe

For the full usage, `see the bottom of this page <#command-usage>`_

Options and argument processing
-------------------------------

| Because of the nature of how Singularity cascades commands and
  sub-commands, argument processing is done with a mandatory order.
  **This means that where you place arguments is important!** In the
  above usage example, ``opts1`` are the global Singularity run-time options.
  These options are always applicable no matter what subcommand you
  select (e.g. ``--verbose`` or ``--debug`` ). But subcommand specific options must be passed
  after the relevant subcommand.
| To further clarify this example, the ``exec`` Singularity subcommand will
  execute a program within the container and pass the arguments passed
  to the program. So to mitigate any argument clashes, Singularity must
  not interpret or interfere with any of the command arguments or
  options that are not relevant for that particular function.

Singularity Help
----------------

Singularity comes with some internal documentation by using the ``help``
subcommand followed by the subcommand you want more information about.
For example:

::

    $ singularity help create
    CREATE OPTIONS:
        -s/--size   Specify a size for an operation in MiB, i.e. 1024*1024B
                    (default 768MiB)
        -F/--force  Overwrite an image file if it exists

    EXAMPLES:

        $ singularity create /tmp/Debian.img
        $ singularity create -s 4096 /tmp/Debian.img

    For additional help, please visit our public documentation pages which are
    found at:

        https://www.sylabs.io/docs/

Commands Usage
==============

.. _sec:commandsusage:

::

    USAGE: singularity [global options...] <command> [command options...] ...

    GLOBAL OPTIONS:
        -d|--debug    Print debugging information
        -h|--help     Display usage summary
        -s|--silent   Only print errors
        -q|--quiet    Suppress all normal output
           --version  Show application version
        -v|--verbose  Increase verbosity +1
        -x|--sh-debug Print shell wrapper debugging information

    GENERAL COMMANDS:
        help       Show additional help for a command or container
        selftest   Run some self tests for singularity install

    CONTAINER USAGE COMMANDS:
        exec       Execute a command within container
        run        Launch a runscript within container
        shell      Run a Bourne shell within container
        test       Launch a testscript within container

    CONTAINER MANAGEMENT COMMANDS:
        apps       List available apps within a container
        bootstrap  *Deprecated* use build instead
        build      Build a new Singularity container
        check      Perform container lint checks
        inspect    Display a container's metadata
        mount      Mount a Singularity container image
        pull       Pull a Singularity/Docker container to $PWD

    COMMAND GROUPS:
        image      Container image command group
        instance   Persistent instance command group


    CONTAINER USAGE OPTIONS:
        see singularity help <command>

    For any additional help or support visit the Singularity
    website: https://www.sylabs.io/contact/

Support
=======

Have a question, or need further information? `Reach out to us`_.

-----
build
-----

.. _sec:build:

Use ``build`` to download and assemble existing containers, convert containers
from one format to another, or build a container from a `Singularity recipe <#container-recipes>`_.

Overview
========

| The ``build`` command accepts a target as input and produces a container as
  output. The target can be a Singularity Hub or Docker Hub URI, a path
  to an existing container, or a path to a Singularity Recipe file. The
  output container can be in squashfs, ext3, or directory format.
| For a complete list of ``build`` options type ``singularity help build``. For more info on building
  containers see `Build a Container <#build-a-container>`_.

Examples
========

Download an existing container from Singularity Hub or Docker Hub
-----------------------------------------------------------------

::

    $ singularity build lolcow.simg shub://GodloveD/lolcow
    $ singularity build lolcow.simg docker://godlovedc/lolcow

Create --writable images and --sandbox directories
--------------------------------------------------

::

    $ sudo singularity build --writable lolcow.img shub://GodloveD/lolcow
    $ sudo singularity build --sandbox lolcow/ shub://GodloveD/lolcow

Convert containers from one format to another
---------------------------------------------

You can convert the three supported container formats using any
combination.

::

    $ sudo singularity build --writable development.img production.simg
    $ singularity build --sandbox development/ production.simg
    $ singularity build production2 development/

Build a container from a Singularity recipe
-------------------------------------------

Given a Singularity Recipe called ``Singularity`` :

::

    $ sudo singularity build lolcow.simg Singularity

----
exec
----

.. _sec:exec:

The ``exec`` Singularity sub-command allows you to spawn an arbitrary command
within your container image as if it were running directly on the host
system. All standard IO, pipes, and file systems are accessible via the
command being exec’ed within the container. Note that this exec is
different from the Docker exec, as it does not require a container to be
“running” before using it.

Examples
========

Printing the OS release inside the container
--------------------------------------------

::

    $ singularity exec container.img cat /etc/os-release
    PRETTY_NAME="Debian GNU/Linux 8 (jessie)"
    NAME="Debian GNU/Linux"
    VERSION_ID="8"
    VERSION="8 (jessie)"
    ID=debian
    HOME_URL="http://www.debian.org/"
    SUPPORT_URL="http://www.debian.org/support"
    BUG_REPORT_URL="https://bugs.debian.org/"
    $

Printing the OS release for a running instance
----------------------------------------------

Use the ``instance://<instance name>`` syntax like so:

::

    $ singularity exec instance://my-instance cat /etc/os-release

Runtime Flags
-------------

If you are interested in containing an environment or filesystem
locations, we highly recommend that you look at the ``singularity run help`` and our
documentation on `flags`_ to better customize this command.

Special Characters
------------------

And properly passing along special characters to the program within the
container.

::

    $ singularity exec container.img echo -ne "hello\nworld\n\n"
    hello
    world
    $

| And a demonstration using pipes:

::

    $ cat debian.def | singularity exec container.img grep 'MirrorURL'
    MirrorURL "http://ftp.us.debian.org/debian/"
    $

A Python example
----------------

Starting with the file ``hello.py`` in the current directory with the contents of:

::

    #!/usr/bin/python

    import sys
    print("Hello World: The Python version is %s.%s.%s" % sys.version_info[:3])

Because our home directory is automatically bound into the container,
and we are running this from our home directory, we can easily execute
that script using the Python within the container:

::

    $ singularity exec /tmp/Centos7-ompi.img /usr/bin/python hello.py
    Hello World: The Python version is 2.7.5

We can also pipe that script through the container and into the Python
binary which exists inside the container using the following command:

::

    $ cat hello.py | singularity exec /tmp/Centos7-ompi.img /usr/bin/python
    Hello World: The Python version is 2.7.5

For demonstration purposes, let’s also try to use the latest Python
container which exists in DockerHub to run this script:

::

    $ singularity exec docker://python:latest /usr/local/bin/python hello.py
    library/python:latest
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:fbd06356349dd9fb6af91f98c398c0c5d05730a9996bbf88ff2f2067d59c70c4
    Downloading layer: sha256:644eaeceac9ff6195008c1e20dd693346c35b0b65b9a90b3bcba18ea4bcef071
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:766692404ca72f4e31e248eb82f8eca6b2fcc15b22930ec50e3804cc3efe0aba
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:6a3d69edbe90ef916e1ecd8d197f056de873ed08bcfd55a1cd0b43588f3dbb9a
    Downloading layer: sha256:ff18e19c2db42055e6f34323700737bde3c819b413997cddace2c1b7180d7efd
    Downloading layer: sha256:7b9457ec39de00bc70af1c9631b9ae6ede5a3ab715e6492c0a2641868ec1deda
    Downloading layer: sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
    Downloading layer: sha256:6a5a5368e0c2d3e5909184fa28ddfd56072e7ff3ee9a945876f7eee5896ef5bb
    Hello World: The Python version is 3.5.2

A GPU example
-------------

If your host system has an NVIDIA GPU card and a driver installed you
can leverage the card with the ``--nv`` option. (This example requires a fairly
recent version of the NVIDIA driver on the host system to run the latest
version of TensorFlow.

::

    $ git clone https://github.com/tensorflow/models.git
    $ singularity exec --nv docker://tensorflow/tensorflow:latest-gpu \
        python ./models/tutorials/image/mnist/convolutional.py
    Docker image path: index.docker.io/tensorflow/tensorflow:latest-gpu
    Cache folder set to /home/david/.singularity/docker
    [19/19] |===================================| 100.0%
    Creating container runtime...
    Extracting data/train-images-idx3-ubyte.gz
    Extracting data/train-labels-idx1-ubyte.gz
    Extracting data/t10k-images-idx3-ubyte.gz
    Extracting data/t10k-labels-idx1-ubyte.gz
    2017-08-18 20:33:59.677580: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.
    2017-08-18 20:33:59.677620: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.
    2017-08-18 20:34:00.148531: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:893] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2017-08-18 20:34:00.148926: I tensorflow/core/common_runtime/gpu/gpu_device.cc:955] Found device 0 with properties:
    name: GeForce GTX 760 (192-bit)
    major: 3 minor: 0 memoryClockRate (GHz) 0.8885
    pciBusID 0000:03:00.0
    Total memory: 2.95GiB
    Free memory: 2.92GiB
    2017-08-18 20:34:00.148954: I tensorflow/core/common_runtime/gpu/gpu_device.cc:976] DMA: 0
    2017-08-18 20:34:00.148965: I tensorflow/core/common_runtime/gpu/gpu_device.cc:986] 0:   Y
    2017-08-18 20:34:00.148979: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1045] Creating TensorFlow device (/gpu:0) -> (device: 0, name: GeForce GTX 760 (192-bit), pci bus id: 0000:03:00.0)
    Initialized!
    Step 0 (epoch 0.00), 21.7 ms
    Minibatch loss: 8.334, learning rate: 0.010000
    Minibatch error: 85.9%
    Validation error: 84.6%
    Step 100 (epoch 0.12), 20.9 ms
    Minibatch loss: 3.235, learning rate: 0.010000
    Minibatch error: 4.7%
    Validation error: 7.8%
    Step 200 (epoch 0.23), 20.5 ms
    Minibatch loss: 3.363, learning rate: 0.010000
    Minibatch error: 9.4%
    Validation error: 4.2%
    [...snip...]
    Step 8500 (epoch 9.89), 20.5 ms
    Minibatch loss: 1.602, learning rate: 0.006302
    Minibatch error: 0.0%
    Validation error: 0.9%
    Test error: 0.8%

-------
inspect
-------

.. _sec:inspect:

| How can you sniff an image? We have provided the inspect command for
  you to easily see the runscript, test script, environment, help, and
  metadata labels.
| This command is essential for making containers understandable by
  other tools and applications.

JSON Api Standard
=================

For any inspect command, by adding –json you can be assured to get a
JSON API standardized response, for example:

::

    singularity inspect -l --json ubuntu.img
    {
        "data": {
            "attributes": {
                "labels": {
                    "SINGULARITY_DEFFILE_BOOTSTRAP": "docker",
                    "SINGULARITY_DEFFILE": "Singularity",
                    "SINGULARITY_BOOTSTRAP_VERSION": "2.2.99",
                    "SINGULARITY_DEFFILE_FROM": "ubuntu:latest"
                }
            },
            "type": "container"
        }
    }

Inspect Flags
=============

The default, if run without any arguments, will show you the container
labels file

::

    $ singularity inspect ubuntu.img
    {
        "SINGULARITY_DEFFILE_BOOTSTRAP": "docker",
        "SINGULARITY_DEFFILE": "Singularity",
        "SINGULARITY_BOOTSTRAP_VERSION": "2.2.99",
        "SINGULARITY_DEFFILE_FROM": "ubuntu:latest"
    }

and as outlined in the usage, you can specify to see any combination of ``--labels``
, ``--environment`` , ``--runscript`` , ``--test`` , and ``--deffile``. The quick command to see everything, in json format, would
be:

::

    $ singularity inspect -l -r -d -t -e -j -hf ubuntu.img
    {
        "data": {
            "attributes": {
                "test": null,
                "help": "This is how you run the image!\n",
                "environment": "# Custom environment shell code should follow\n\n",
                "labels": {
                    "SINGULARITY_DEFFILE_BOOTSTRAP": "docker",
                    "SINGULARITY_DEFFILE": "Singularity",
                    "SINGULARITY_BOOTSTRAP_VERSION": "2.2.99",
                    "SINGULARITY_DEFFILE_FROM": "ubuntu:latest"
                },
                "deffile": "Bootstrap:docker\nFrom:ubuntu:latest\n",
                "runscript": "#!/bin/sh\n\nexec /bin/bash \"$@\""
            },
            "type": "container"
        }
    }

Labels
------

The default, if run without any arguments, will show you the container
labels file (located at ``/.singularity.d/labels.json`` in the container. These labels are the ones that
you define in the ``%labels`` section of your bootstrap file, along with any Docker ``LABEL``
that came with an image that you imported, and other metadata about the
bootstrap. For example, here we are inspecting labels for ``ubuntu.img``

::

    $ singularity inspect ubuntu.img
    {
        "SINGULARITY_DEFFILE_BOOTSTRAP": "docker",
        "SINGULARITY_DEFFILE": "Singularity",
        "SINGULARITY_BOOTSTRAP_VERSION": "2.2.99",
        "SINGULARITY_DEFFILE_FROM": "ubuntu:latest"
    }

| This is the equivalent of both of:

::

    $ singularity inspect -l ubuntu.img
    $ singularity inspect --labels ubuntu.img

Runscript
---------

The commands ``--runscript`` or ``--r`` will show you the runscript, which also can be shown in ``--json``
:

::

    $ singularity inspect -r -j ubuntu.img{
        "data": {
            "attributes": {
                "runscript": "#!/bin/sh\n\nexec /bin/bash \"$@\""
            },
            "type": "container"
        }
    }

or in a human friendly, readable print to the screen:

::

    $ singularity inspect -r ubuntu.img

    ##runscript
    #!/bin/sh

    exec /bin/bash "$@"

Help
----

| The commands ``--helpfile`` or ``--hf`` will show you the runscript helpfile, if it exists.
  With ``--json`` you can also see it as such:

::

    singularity inspect -hf -j dino.img
    {
        "data": {
            "attributes": {
                "help": "\n\n\nHi there! This is my image help section.\n\nUsage:\n\nboobeep doo doo\n\n --arg/a arrrrg I'm a pirate!\n --boo/b eeeeeuzzz where is the honey?\n\n\n"
            },
            "type": "container"
        }
    }

or in a human friendly, readable print to the screen, don’t use ``-j`` or ``--json``:

::

    $ singularity inspect -hf dino.img


    Hi there! This is my image help section.

    Usage:

    boobeep doo doo

     --arg/a arrrrg I'm a pirate!
     --boo/b eeeeeuzzz where is the honey?

Environment
-----------

The commands ``--environment`` and ``-e`` will show you the container’s environment, again
specified by the ``%environment`` section of a bootstrap file, and other ENV labels that
might have come from a Docker import. You can again choose to see ``--json`` :

::

    $ singularity inspect -e --json ubuntu.img
    {
        "data": {
            "attributes": {
                "environment": "# Custom environment shell code should follow\n\n"
            },
            "type": "container"
        }
    }

or human friendly:

::

    $ singularity inspect -e ubuntu.img

    ##environment
    # Custom environment shell code should follow

The container in the example above did not have any custom environment
variables set.

Test
----

| The equivalent ``--test`` or ``-t`` commands will print any test defined for the
  container, which comes from the  ``%test`` section of the bootstrap specification
  Singularity file. Again, we can ask for ``--json`` or human friendly (default):

::

    $ singularity --inspect -t --json ubuntu.img
    {
        "data": {
            "attributes": {
                "test": null
            },
            "type": "container"
        }
    }

    $ singularity inspect -t  ubuntu.img
    {
        "status": 404,
        "detail": "This container does not have any tests defined",
        "title": "Tests Undefined"
    }

Deffile
-------

Want to know where your container came from? You can see the entire
Singularity definition file, if the container was created with a
bootstrap, by using ``--deffile`` or ``-d``:

::

    $ singularity inspect -d  ubuntu.img

    ##deffile
    Bootstrap:docker
    From:ubuntu:latest

or with ``--json`` output.

::

    $ singularity inspect -d --json ubuntu.img
    {
        "data": {
            "attributes": {
                "deffile": "Bootstrap:docker\nFrom:ubuntu:latest\n"
            },
            "type": "container"
        }
    }

The goal of these commands is to bring more transparency to containers,
and to help better integrate them into common workflows by having them
expose their guts to the world! If you have feedback for how we can
improve or amend this, `please let us know`_!

----
pull
----

.. _sec:pull:

| Singularity ``pull`` is the command that you would want to use to communicate
  with a container registry. The command does exactly as it says - there
  exists an image external to my host, and I want to pull it here. We
  currently support pull for both `Docker <https://hub.docker.com/>`_ and `Singularity Hub
  images`_, and will review usage for both.

Singularity Hub
===============

Singularity differs from Docker in that we serve entire images, as
opposed to layers. This means that pulling a Singularity Hub means
downloading the entire (compressed) container file, and then having it
extract on your local machine. The basic command is the following:

::

    singularity pull shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: ./vsoch-hello-world-master.img

How do tags work?
-----------------

On Singularity Hub, a ``tag`` coincide with a branch. So if you have a repo
called ``vsoch/hello-world`` , by default the file called ``Singularity`` (your build recipe file) will be
looked for in the base of the master branch. The command that we issued
above would be equivalent to doing:

::

    singularity pull shub://vsoch/hello-world:master

To enable other branches to build, they must be turned on in your
collection (more details are available in the `Singularity Hub docs`_).
If you then put another Singularity file in a branch called development,
you would pull it as follows:

::

    singularity pull shub://vsoch/hello-world:development

The term ``latest`` in Singularity Hub will pull, across all of your
branches, the most recent image. If ``development`` is more recent than
``master``, it would be pulled, for example.

Image Names
-----------

As you can see, since we didn’t specify anything special, the default
naming convention is to use the username, reponame, and the branch
(tag). You have three options for changing this:

::

    PULL OPTIONS:
        -n/--name   Specify a custom container name (first priority)
        -C/--commit Name container based on GitHub commit (second priority)
        -H/--hash   Name container based on file hash (second priority)

Custom Name
-----------

::

    singularity pull --name meatballs.img shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: ./meatballs.img

Name by commit
--------------

Each container build on Singularity Hub is associated with the GitHub
commit of the repo that was used to build it. You can specify to name
your container based on the commit with the ``--commit`` flag, if, for example, you
want to match containers to their build files:

::

    singularity pull --commit shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: ./4187993b8b44cbfa51c7e38e6b527918fcdf0470.img

Name by hash
------------

If you prefer the hash of the file itself, you can do that too.

::

    singularity pull --hash shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: ./4db5b0723cfd378e332fa4806dd79e31.img

Pull to different folder
------------------------

| For any of the above, if you want to specify a different folder for
  your image, you can define the variable ``SINGULARITY_PULLFOLDER``. By default, we will first
  check if you have the ``SINGULARITY_CACHEDIR`` defined, and pull images there. If not, we look
  for ``SINGULARITY_PULLFOLDER``. If neither of these are defined, the image is pulled to the
  present working directory, as we showed above. Here is an example of
  pulling to ``/tmp`` .

::

    SINGULARITY_PULLFOLDER=/tmp
    singularity pull shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: /tmp/vsoch-hello-world-master.img

Pull by commit
--------------

| You can also pull different versions of your container by using their
  commit id ( ``version`` ).

::

    singularity pull shub://vsoch/hello-world@42e1f04ed80217895f8c960bdde6bef4d34fab59
    Progress |===================================| 100.0%
    Done. Container is at: ./vsoch-hello-world-master.img

In this example, the first build of this container will be pulled.

Docker
======

Docker pull is similar (on the surface) to a Singularity Hub pull, and
we would do the following:

::

    singularity pull docker://ubuntu
    Initializing Singularity image subsystem
    Opening image file: ubuntu.img
    Creating 223MiB image
    Binding image to loop
    Creating file system within image
    Image is done: ubuntu.img
    Docker image path: index.docker.io/library/ubuntu:latest
    Cache folder set to /home/vanessa/.singularity/docker
    Importing: base Singularity environment
    Importing: /home/vanessa/.singularity/docker/sha256:b6f892c0043b37bd1834a4a1b7d68fe6421c6acbc7e7e63a4527e1d379f92c1b.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:55010f332b047687e081a9639fac04918552c144bc2da4edb3422ce8efcc1fb1.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:2955fb827c947b782af190a759805d229cfebc75978dba2d01b4a59e6a333845.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:3deef3fcbd3072b45771bd0d192d4e5ff2b7310b99ea92bce062e01097953505.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:cf9722e506aada1109f5c00a9ba542a81c9e109606c01c81f5991b1f93de7b66.tar.gz
    Importing: /home/vanessa/.singularity/metadata/sha256:fe44851d529f465f9aa107b32351c8a0a722fc0619a2a7c22b058084fac068a4.tar.gz
    Done. Container is at: ubuntu.img

If you specify the tag, the image would be named accordingly (eg, ``ubuntu-latest.img``). Did
you notice that the output looks similar to if we did the following?

::

    singularity create ubuntu.img
    singularity import ubuntu.img docker://ubuntu

this is because the same logic is happening on the back end. Thus, the
pull command with a docker uri also supports arguments ``--size`` and ``--name`` . Here is how I
would pull an ubuntu image, but make it bigger, and name it something
else.

::

    singularity pull --size 2000 --name jellybelly.img docker://ubuntu
    Initializing Singularity image subsystem
    Opening image file: jellybelly.img
    Creating 2000MiB image
    Binding image to loop
    Creating file system within image
    Image is done: jellybelly.img
    Docker image path: index.docker.io/library/ubuntu:latest
    Cache folder set to /home/vanessa/.singularity/docker
    Importing: base Singularity environment
    Importing: /home/vanessa/.singularity/docker/sha256:b6f892c0043b37bd1834a4a1b7d68fe6421c6acbc7e7e63a4527e1d379f92c1b.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:55010f332b047687e081a9639fac04918552c144bc2da4edb3422ce8efcc1fb1.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:2955fb827c947b782af190a759805d229cfebc75978dba2d01b4a59e6a333845.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:3deef3fcbd3072b45771bd0d192d4e5ff2b7310b99ea92bce062e01097953505.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:cf9722e506aada1109f5c00a9ba542a81c9e109606c01c81f5991b1f93de7b66.tar.gz
    Importing: /home/vanessa/.singularity/metadata/sha256:fe44851d529f465f9aa107b32351c8a0a722fc0619a2a7c22b058084fac068a4.tar.gz
    Done. Container is at: jellybelly.img

---
run
---

.. _sec:run:

It’s common to want your container to “do a thing.” Singularity ``run`` allows
you to define a custom action to be taken when a container is either ``run`` or
executed directly by file name. Specifically, you might want it to
execute a command, or run an executable that gives access to many
different functions for the user.

Overview
========

First, how do we run a container? We can do that in one of two ways -
the commands below are identical:

::

    $ singularity run centos7.img
    $ ./centos7.img

In both cases, we are executing the container’s “runscript” (the
executable ``/singularity`` at the root of the image) that is either an actual file
(version 2.2 and earlier) or a link to one (2.3 and later). For example,
looking at a 2.3 image, I can see the runscript via the path to the
link:

::

    $ singularity exec centos7.img cat /singularity
    #!/bin/sh

    exec /bin/bash "$@"

or to the actual file in the container’s metadata folder, ``/.singularity.d``

::

    $ singularity exec centos7.img cat /.singularity.d/runscript
    #!/bin/sh

    exec /bin/bash "$@"

| Notice how the runscript has bash followed by ``\$@`` ? This is good practice
  to include in a runscript, as any arguments passed by the user will be
  given to the container.

Runtime Flags
=============

If you are interested in containing an environment or filesystem
locations, we highly recommend that you look at the ``singularity run help`` and our
documentation on `flags`_ to better customize this command.

Examples
========

| In this example the container has a very simple runscript defined.

::

    $ singularity exec centos7.img cat /singularity
    #!/bin/sh

    echo motorbot

    $ singularity run centos7.img
    motorbot

Defining the Runscript
----------------------

When you first create a container, the runscript is defined using the
following order of operations:

#. A user defined runscript in the ``%runscript`` section of a bootstrap takes
   preference over all

#. If the user has not defined a runscript and is importing a Docker
   container, the Docker ``ENTRYPOINT`` is used.

#. If a user has not defined a runscript and adds ``IncludeCmd: yes`` to the bootstrap file,
   the ``CMD`` is used over the ``ENTRYPOINT``

#. If the user has not defined a runscript and the Docker container
   doesn’t have an ``ENTRYPOINT``, we look for ``CMD``, even if the user hasn’t asked for it.

#. If the user has not defined a runscript, and there is no ``ENTRYPOINT`` or ``CMD`` (or we
   aren’t importing Docker at all) then we default to ``/bin/bash``

Here is how you would define the runscript section when you `build <#build-a-container>`_ an image:

::

    Bootstrap: docker
    From: ubuntu:latest

    %runscript
    exec /usr/bin/python "$@"

and of course python should be installed as /usr/bin/python. The
addition of ``$@`` ensures that arguments are passed along from the user. If
you want your container to run absolutely any command given to it, and
you want to use run instead of exec, you could also just do:

::

    Bootstrap: docker
    From: ubuntu:latest

    %runscript
    exec "$@"`

If you want different entrypoints for your image, we recommend using the
%apprun syntax (see `apps <#reproducible-sci-f-apps>`_). Here we have two entrypoints for foo and bar:

::

    %runscript
    exec echo "Try running with --app dog/cat"

    %apprun dog
    exec echo Hello "$@", this is Dog

    %apprun cat
    exec echo Meow "$@", this is Cat

and then running (after build of a complete recipe) would look like:

::

    sudo singularity build catdog.simg Singularity

    $ singularity run catdog.simg
    Try running with --app dog/cat

    $ singularity run --app cat catdog.simg
     Meow , this is Cat
    $ singularity run --app dog catdog.simg
    Hello , this is Dog

Generally, it is advised to provide help for your container with ``%help`` or ``%apphelp``. If
you find it easier, you can also provide help by way of a runscript that
tells your user how to use the container, and gives access to the
important executables. Regardless of your strategy. a reproducible
container is one that tells the user how to interact with it.

-----
shell
-----

.. _sec:shell:

The ``shell`` Singularity sub-command will automatically spawn an interactive
shell within a container. As of v2.3 the default that is spawned via the
shell command is ``/bin/bash`` if it exists otherwise ``/bin/sh`` is called.

::

    $ singularity shell
    USAGE: singularity (options) shell [container image] (options)

Here we can see the default shell in action:

::

    $ singularity shell centos7.img
    Singularity: Invoking an interactive shell within container...

    Singularity centos7.img:~> echo $SHELL
    /bin/bash

Additionally any arguments passed to the Singularity command (after the
container name) will be passed to the called shell within the container,
and shell can be used across image types. Here is a quick example of
shelling into a container assembled from Docker layers. We highly
recommend that you look at the ``singularity shell help`` and our documentation on `flags`_ to
better customize this command.

Change your shell
=================

The ``shell`` sub-command allows you to set or change the default shell using the ``--shell``
argument. As of Singularity version 2.2, you can also use the
environment variable ``SINGULARITY_SHELL`` which will use that as your shell entry point into
the container.

Bash
----

| The correct way to do it:

::

    export SINGULARITY_SHELL="/bin/bash --norc"
    singularity shell centos7.img Singularity: Invoking an interactive shell within container...
    Singularity centos7.img:~/Desktop> echo $SHELL
    /bin/bash --norc

Don’t do this, it can be confusing:

::

    $ export SINGULARITY_SHELL=/bin/bash
    $ singularity shell centos7.img
    Singularity: Invoking an interactive shell within container...

    # What? We are still on my Desktop? Actually no, but the uri says we are!
    vanessa@vanessa-ThinkPad-T460s:~/Desktop$ echo $SHELL
    /bin/bash

Depending on your shell, you might also want the ``--noprofile`` flag. How can you learn
more about a shell? Ask it for help, of course!

Shell Help
==========

::

    $ singularity shell centos7.img --help
    Singularity: Invoking an interactive shell within container...

    GNU bash, version 4.2.46(1)-release-(x86_64-redhat-linux-gnu)
    Usage:  /bin/bash [GNU long option] [option] ...
        /bin/bash [GNU long option] [option] script-file ...
    GNU long options:
        --debug
        --debugger
        --dump-po-strings
        --dump-strings
        --help
        --init-file
        --login
        --noediting
        --noprofile
        --norc
        --posix
        --protected
        --rcfile
        --rpm-requires
        --restricted
        --verbose
        --version
    Shell options:
        -irsD or -c command or -O shopt_option      (invocation only)
        -abefhkmnptuvxBCHP or -o option
    Type `/bin/bash -c "help set"' for more information about shell options.
    Type `/bin/bash -c help' for more information about shell builtin commands.

| And thus we should be able to do:

::

    $ singularity shell centos7.img -c "echo hello world"
    Singularity: Invoking an interactive shell within container...

    hello world


    .. _Singularity Hub: https://singularity-hub.org/
    .. _Docker Hub: https://hub.docker.com/
    .. _Singularity Registry: https://www.github.com/singularityhub/sregistry
    .. _reach out!: https://www.sylabs.io/contact/
    .. _Reach out to us: https://www.sylabs.io/bug-report/
    .. _GitHub repo: https://github.com/singularityware/singularity
    .. _GitHub releases: https://github.com/singularityware/singularity/releases
    .. _here: https://sci-f.github.io/tutorials
    .. _this guide: https://github.com/singularityhub/singularityhub.github.io/wiki
    .. _defaults.py: https://github.com/singularityware/singularity/blob/master/libexec/python/defaults.py
    .. _manifest list: https://docs.docker.com/registry/spec/manifest-v2-2/#manifest-list
    .. _Scientific Filesystem: https://sci-f.github.io/
    .. _examples: https://github.com/singularityware/singularity/tree/master/examples
    .. _Singularity source code: https://github.com/singularityware/singularity
    .. _shub: http://singularity-userdoc.readthedocs.io/en/latest/#build-shub
    .. _docker: http://singularity-userdoc.readthedocs.io/en/latest/#build-docker-module
    .. _localimage: http://singularity-userdoc.readthedocs.io/en/latest/#build-localimage
    .. _yum: http://singularity-userdoc.readthedocs.io/en/latest/#build-yum
    .. _debootstrap: http://singularity-userdoc.readthedocs.io/en/latest/#build-debootstrap
    .. _arch: http://singularity-userdoc.readthedocs.io/en/latest/#build-arch
    .. _busybox: http://singularity-userdoc.readthedocs.io/en/latest/#build-busybox
    .. _zypper: http://singularity-userdoc.readthedocs.io/en/latest/#build-zypper
    .. _same conventions apply: https://linux.die.net/man/1/cp
    .. _Standard Container Integration Format: https://sci-f.github.io/
    .. _SCI-F Apps Home: https://sci-f.github.io/
    .. _squashfs image: https://en.wikipedia.org/wiki/SquashFS
    .. _singularity hub: https://github.com/singularityhub/singularityhub.github.io/wiki
    .. _enabled by the system administrator: https://singularity-admindoc.readthedocs.io/en/latest/#parameters
    .. _enabled user control of binds: https://singularity-admindoc.readthedocs.io/en/latest/#parameters
    .. _overlay in the Singularity configuration file: https://singularity-admindoc.readthedocs.io/en/latest/#parameters
    .. _here on GitHub: https://github.com/bauerm97/instance-example
    .. _here on SingularityHub: https://singularity-hub.org/collections/bauerm97/instance-example/
    .. _Puppeteer: https://github.com/GoogleChrome/puppeteer
    .. _tell us!: https://github.com/singularityware/singularity/issues
    .. _rc1 Label Schema: http://label-schema.org/rc1/
    .. _scientific filesystem: https://sci-f.github.io/
    .. _cowsay container: https://github.com/singularityware/singularity/blob/development/examples/apps/Singularity.cowsay
    .. _GodLoveD: https://www.github.com/GodLoveD
    .. _full documentation: https://sci-f.github.io/
    .. _take a look at these examples: https://asciinema.org/a/139153?speed=3
    .. _Docker image folder: http://stackoverflow.com/questions/19234831/where-are-docker-images-stored-on-the-host-machine
    .. _Docker Remote API: https://docs.docker.com/engine/reference/api/docker_remote_api/
    .. _let us know: https://www.github.com/singularityware/singularityware.github.io/issues
    .. _ldconfig: https://codeyarns.com/2014/01/14/how-to-add-library-directory-to-ldconfig-cache/
    .. _ping us an issue: https://www.github.com/singularityware/singularity/issues
    .. _security implications: https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/admin-guide/kernel-parameters.txt?h=v4.13-rc3#n4387
    .. _original issue: https://github.com/singularityware/singularity/issues/845
    .. _run into this issue: https://github.com/singularityware/singularity/issues/476
    .. _yarikoptic: https://github.com/yarikoptic
    .. _flags: http://singularity-userdoc.readthedocs.io/en/latest/#singularity-action-flags
    .. _please let us know: https://github.com/singularityware/singularity/issues
    .. _Docker: https://hub.docker.com/
    .. _Singularity Hub images: https://singularity-hub.org/
    .. _Singularity Hub docs: https://singularity-hub.org/faq
    .. _ext3: https://en.wikipedia.org/wiki/Ext3

    .. |Singularity workflow| image:: flow.png
