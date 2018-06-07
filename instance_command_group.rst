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
