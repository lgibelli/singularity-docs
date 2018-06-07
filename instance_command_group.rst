======================
Instance Command Group
======================

.. _sec:instances:

--------------
instance.start
--------------

.. _sec:instancestart:

New in Singularity version 2.4 you can use the ``instance`` command group to run
instances of containers in the background. This is useful for running
services like databases and web servers. The ``instance.start`` command lets you initiate a
named instance in the background.

Overview
========

To initiate a named instance of a container, you must call the ``instance.start`` command
with 2 arguments: the name of the container that you want to start and a
unique name for an instance of that container. Once the new instance is
running, you can join the container’s namespace using a URI style syntax
like so:

::

    $ singularity shell instance://<instance_name>

| You can specify options such as bind mounts, overlays, or custom
  namespaces when you initiate a new instance of a container with
  instance.start. These options will persist as long as the container
  runs.
| For a complete list of options see the output of:

::

    singularity help instance.start

Examples
========

These examples use a container from Singularity Hub, but you can use
local containers or containers from Docker Hub as well. For a more
detailed look at ``instance`` usage see `Running Instances <#why-container-instances>`_.

Start an instance called cow1 from a container on Singularity Hub
-----------------------------------------------------------------

::

    $ singularity instance.start shub://GodloveD/lolcow cow1

Start an interactive shell within the instance that you just started
--------------------------------------------------------------------

::

    $ singularity shell instance://cow1
    Singularity GodloveD-lolcow-master.img:~> ps -ef
    UID        PID  PPID  C STIME TTY          TIME CMD
    ubuntu       1     0  0 20:03 ?        00:00:00 singularity-instance: ubuntu [cow1]
    ubuntu       3     0  0 20:04 pts/0    00:00:00 /bin/bash --norc
    ubuntu       4     3  0 20:04 pts/0    00:00:00 ps -ef
    Singularity GodloveD-lolcow-master.img:~> exit

Execute the runscript within the instance
-----------------------------------------

::

    $ singularity run instance://cow1
     _________________________________________
    / Clothes make the man. Naked people have \
    | little or no influence on society.      |
    |                                         |
    \ -- Mark Twain                           /
     -----------------------------------------
            \   ^__^
             \  (oo)\_______
                (__)\       )\/\
                    ||----w |
                    ||     ||

Run a command within a running instance
---------------------------------------

::

    $ singularity exec instance://cow1 cowsay "I like blending into the background"
     _____________________________________
    < I like blending into the background >
     -------------------------------------
            \   ^__^
             \  (oo)\_______
                (__)\       )\/\
                    ||----w |
                    ||     ||

-------------
instance.list
-------------

.. _sec:instancelist:

New in Singularity version 2.4 you can use the ``instance`` command group to run
instances of containers in the background. This is useful for running
services like databases and web servers. The ``instance.list`` command lets you keep track
of the named instances running in the background.

Overview
========

After initiating one or more named instances to run in the background
with the ``instance.start`` command you can list them with the ``instance.list`` command.

Examples
========

These examples use a container from Singularity Hub, but you can use
local containers or containers from Docker Hub as well. For a more
detailed look at ``instance`` usage see `Running Instances <#why-container-instances>`_.

Start a few named instances from containers on Singularity Hub
--------------------------------------------------------------

::

    $ singularity instance.start shub://GodloveD/lolcow cow1
    $ singularity instance.start shub://GodloveD/lolcow cow2
    $ singularity instance.start shub://vsoch/hello-world hiya

List running instances
----------------------

::

    $ singularity instance.list
    DAEMON NAME      PID      CONTAINER IMAGE
    cow1             20522    /home/ubuntu/GodloveD-lolcow-master.img
    cow2             20558    /home/ubuntu/GodloveD-lolcow-master.img
    hiya             20595    /home/ubuntu/vsoch-hello-world-master.img

-------------
instance.stop
-------------

.. _sec:instancestop:

New in Singularity version 2.4 you can use the ``instance`` command group to run
instances of containers in the background. This is useful for running
services like databases and web servers. The ``instance.stop`` command lets you stop
instances once you are finished using them

Overview
========

After initiating one or more named instances to run in the background
with the ``instance.start`` command you can stop them with the ``instance.stop`` command.

Examples
========

These examples use a container from Singularity Hub, but you can use
local containers or containers from Docker Hub as well. For a more
detailed look at ``instance`` usage see `Running Instances <#why-container-instances>`_.

Start a few named instances from containers on Singularity Hub
--------------------------------------------------------------

::

    $ singularity instance.start shub://GodloveD/lolcow cow1
    $ singularity instance.start shub://GodloveD/lolcow cow2
    $ singularity instance.start shub://vsoch/hello-world hiya

Stop a single instance
----------------------

::

    $ singularity instance.stop cow1
    Stopping cow1 instance of /home/ubuntu/GodloveD-lolcow-master.img (PID=20522)

Stop all running instances
--------------------------

::

    $ singularity instance.stop --all
    Stopping cow2 instance of /home/ubuntu/GodloveD-lolcow-master.img (PID=20558)
    Stopping hiya instance of /home/ubuntu/vsoch-hello-world-master.img (PID=20595)
