********
Appendix
********

build-docker-module
-------------------

.. _sec:build-docker-module:

Overview
~~~~~~~~

Docker images are comprised of layers that are assembled at runtime to create an image. You can use Docker layers to create a base
image, and then add your own custom software. For example, you might use Docker’s Ubuntu image layers to create an Ubuntu Singularity
container. You could do the same with CentOS, Debian, Arch, Suse, Alpine, BusyBox, etc.

Or maybe you want a container that already has software installed. For instance, maybe you want to build a container that uses CUDA
and cuDNN to leverage the GPU, but you don’t want to install from scratch. You can start with one of the ``nvidia/cuda`` containers and
install your software on top of that.

Or perhaps you have already invested in Docker and created your own Docker containers. If so, you can seamlessly convert them to
Singularity with the ``docker`` bootstrap module.

Keywords
~~~~~~~~

::

    Bootstrap: docker

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    From: <registry>/<namespace>/<container>:<tag>@<digest>

The From keyword is mandatory. It specifies the container to use as a base. ``registry`` is optional and defaults to ``index.docker.io``.
``namespace`` is optional and defaults to ``library``. This is the correct namespace to use for some official containers (ubuntu for example).
``tag`` is also optional and will default to ``latest``

See `Singularity and Docker <#singularity-and-docker>`_ for more detailed info on using Docker registries.

::

    Registry: http://custom_registry

The Registry keyword is optional. It will default to ``index.docker.io``.

::

    Namespace: namespace

The Namespace keyword is optional. It will default to ``library``.

::

    IncludeCmd: yes

The IncludeCmd keyword is optional. If included, and if a ``%runscript`` is not specified, a Docker ``CMD`` will take precedence over ``ENTRYPOINT``
and will be used as a runscript. Note that the ``IncludeCmd`` keyword is considered valid if it is not empty! This means that
 ``IncludeCmd: yes`` and ``IncludeCmd: no`` are identical. In both cases the ``IncludeCmd`` keyword is not empty, so the Docker ``CMD`` will take precedence
 over an ``ENTRYPOINT``.

 See `Singularity and Docker <#singularity-and-docker>`_ for more info on order of operations for determining a runscript.


Notes
~~~~~

Docker containers are stored as a collection of tarballs called layers. When building from a Docker container the layers must be downloaded and then
assembled in the proper order to produce a viable file system. Then the file system must be converted to squashfs or ext3 format.

Building from Docker Hub is not considered reproducible because if any of the layers of the image are changed, the container will change.
If reproducibility is important to you, consider hosting a base container on Singularity Hub and building from it instead.

For detailed information about setting your build environment see `Build Customization <#id15>`_.

build-shub
----------

.. _sec:build-shub:

Overview
~~~~~~~~

You can use an existing container on Singularity Hub as your “base,” and then add customization. This allows you to build multiple images
from the same starting point. For example, you may want to build several containers with the same custom python installation, the same custom
compiler toolchain, or the same base MPI installation. Instead of building these from scratch each time, you could create a base container on
Singularity Hub and then build new containers from that existing base container adding customizations in ``%post`` , ``%environment``, ``%runscript``, etc.

Keywords
~~~~~~~~

::

    Bootstrap: shub

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    From: shub://<registry>/<username>/<container-name>:<tag>@digest

The From keyword is mandatory. It specifies the container to use as a base. ``registry is optional and defaults to ``singularity-hub.org``.
``tag`` and ``digest`` are also optional. ``tag`` defaults to ``latest`` and ``digest`` can be left blank if you want the latest build.

Notes
~~~~~

When bootstrapping from a Singularity Hub image, all previous definition files that led to the creation of the current image will be stored
in a directory within the container called ``/.singularity.d/bootstrap_history``. Singularity will also alert you if environment variables have
been changed between the base image and the new image during bootstrap.


build-localimage
----------------

.. _sec:build-localimage:

This module allows you to build a container from an existing Singularity container on your host system. The name is somewhat misleading
because your container can be in either image or directory format.

Overview
~~~~~~~~

You can use an existing container image as your “base,” and then add customization. This allows you to build multiple images from the same
starting point. For example, you may want to build several containers with the same custom python installation, the same custom compiler
toolchain, or the same base MPI installation. Instead of building these from scratch each time, you could start with the appropriate local
base container and then customize the new container in ``%post``, ``%environment``, ``%runscript``, etc.

Keywords
~~~~~~~~

::

    Bootstrap: localimage

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    From: /path/to/container/file/or/directory

The From keyword is mandatory. It specifies the local container to use as a base.

Notes
~~~~~

When building from a local container, all previous definition files that led to the creation of the current container will be stored in a
directory within the container called ``/.singularity.d/bootstrap_history``. Singularity will also alert you if environment variables have been
changed between the base image and the new image during bootstrap.


build-yum
---------

.. _sec:build-yum:

This module allows you to build a Red Hat/CentOS/Scientific Linux style container from a mirror URI.

Overview
~~~~~~~~

Use the ``yum`` module to specify a base for a CentOS-like container. You must also specify the URI for the mirror you would like to use.

Keywords
~~~~~~~~

::

    Bootstrap: yum

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    OSVersion: 7

The OSVersion keyword is optional. It specifies the OS version you would like to use. It is only required if you have specified a %{OSVERSION}
variable in the ``MirrorURL`` keyword.

::

    MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/

The MirrorURL keyword is mandatory. It specifies the URL to use as a mirror to download the OS. If you define the ``OSVersion`` keyword, than you
can use it in the URL as in the example above.

::

    Include: yum

The Include keyword is optional. It allows you to install additional packages into the core operating system. It is a best practice to supply
only the bare essentials such that the ``%post`` section has what it needs to properly complete the build. One common package you may want to install
when using the ``yum`` build module is YUM itself.

Notes
~~~~~

There is a major limitation with using YUM to bootstrap a container. The RPM database that exists within the container will be created using the
RPM library and Berkeley DB implementation that exists on the host system. If the RPM implementation inside the container is not compatible with
the RPM database that was used to create the container, RPM and YUM commands inside the container may fail. This issue can be easily demonstrated
by bootstrapping an older RHEL compatible image by a newer one (e.g. bootstrap a Centos 5 or 6 container from a Centos 7 host).

In order to use the ``debootstrap`` build module, you must have ``yum`` installed on your system. It may seem counter-intuitive to install YUM on a system
that uses a different package manager, but you can do so. For instance, on Ubuntu you can install it like so:

::

    $ sudo apt-get update && sudo apt-get install yum


build-debootstrap
-----------------

.. _sec:build-debootstrap:

This module allows you to build a Debian/Ubuntu style container from a mirror URI.

Overview
~~~~~~~~

Use the ``debootstrap`` module to specify a base for a Debian-like container. You must also specify the OS version and a URI for the mirror you would like to use.


Keywords
~~~~~~~~

::

    Bootstrap: debootstrap

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    OSVersion: xenial

The OSVersion keyword is mandatory. It specifies the OS version you would like to use. For Ubuntu you can use code words like ``trusty`` (14.04), ``xenial`` (16.04),
and ``yakkety`` (17.04). For Debian you can use values like ``stable``, ``oldstable``, ``testing``, and ``unstable`` or code words like ``wheezy`` (7), ``jesse`` (8), and ``stretch`` (9).

 ::

     MirrorURL:  http://us.archive.ubuntu.com/ubuntu/

The MirrorURL keyword is mandatory. It specifies a URL to use as a mirror when downloading the OS.

::

    Include: somepackage

The Include keyword is optional. It allows you to install additional packages into the core operating system. It is a best practice to supply only the bare essentials
such that the ``%post`` section has what it needs to properly complete the build.

Notes
~~~~~

In order to use the ``debootstrap`` build module, you must have ``debootstrap`` installed on your system. On Ubuntu you can install it like so:

::

    $ sudo apt-get update && sudo apt-get install debootstrap

On CentOS you can install it from the epel repos like so:

::

    $ sudo yum update && sudo yum install epel-release && sudo yum install debootstrap.noarch


build-arch
----------

.. _sec:build-arch:

This module allows you to build a Arch Linux based container.

Overview
~~~~~~~~

Use the ``arch`` module to specify a base for an Arch Linux based container. Arch Linux uses the aptly named the ``pacman`` package manager (all puns intended).


Keywords
~~~~~~~~

::

    Bootstrap: arch

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

The Arch Linux bootstrap module does not name any additional keywords at this time. By defining the ``arch`` module, you have essentially given all of the
information necessary for that particular bootstrap module to build a core operating system.

Notes
~~~~~

Arch Linux is, by design, a very stripped down, light-weight OS. You may need to perform a fair amount of configuration to get a usable OS. Please refer
to this `README.md <https://github.com/singularityware/singularity/blob/master/examples/arch/README.md>`_ and
the `Arch Linux example <https://github.com/singularityware/singularity/blob/master/examples/arch/Singularity>`_ for more info.

build-busybox
-------------

.. _sec:build-busybox:

This module allows you to build a container based on BusyBox.

Overview
~~~~~~~~

Use the ``busybox`` module to specify a BusyBox base for container. You must also specify a URI for the mirror you would like to use.

Keywords
~~~~~~~~

::

    Bootstrap: busybox

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    MirrorURL: https://www.busybox.net/downloads/binaries/1.26.1-defconfig-multiarch/busybox-x86_64

The MirrorURL keyword is mandatory. It specifies a URL to use as a mirror when downloading the OS.

Notes
~~~~~

You can build a fully functional BusyBox container that only takes up ~600kB of disk space!

build-zypper
------------

.. _sec:build-zypper:

This module allows you to build a Suse style container from a mirror URI.

Overview
~~~~~~~~

Use the ``zypper`` module to specify a base for a Suse-like container. You must also specify a URI for
the mirror you would like to use.

Keywords
~~~~~~~~

::

    Bootstrap: zypper

The Bootstrap keyword is always mandatory. It describes the bootstrap module to use.

::

    OSVersion: 42.2

The OSVersion keyword is optional. It specifies the OS version you would like to use.
It is only required if you have specified a %{OSVERSION} variable in the ``MirrorURL`` keyword.

::

    Include: somepackage

The Include keyword is optional. It allows you to install additional packages into the core operating system.
It is a best practice to supply only the bare essentials such that the ``%post`` section has what it needs to properly complete the build.
One common package you may want to install when using the zypper build module is ``zypper`` itself.

Singularity Action Flags
------------------------
.. _sec:action-flags:

For each of ``exec``, ``run``, and ``shell``, there are a few important flags that we want to note for new users that have substantial impact on using
your container. While we won’t include the complete list of run options (for this complete list see ``singularity run --help`` or more generally
``singularity <action> --help``) we will review some highly useful flags that you can add to these actions.

-  **--contain**: Contain suggests that we want to better isolate the container runtime from the host. Adding the ``--contain`` flag will use minimal
``/dev`` and empty other directories (e.g., ``/tmp``).

-  **--containall**: In addition to what is provided with ``--contain`` (filesystems) also contain PID, IPC, and environment.

-  **--cleanenv**: Clean the environment before running the container.

-  **--pwd**: Initial working directory for payload process inside the container.

This is **not** a complete list! Please see the ``singularity <action> help`` for an updated list.


Examples
~~~~~~~~

Here we are cleaning the environment. In the first command, we see that the variable ``PEANUTBUTTER`` gets passed into the container.

::

    PEANUTBUTTER=JELLY singularity exec Centos7.img env | grep PEANUT
    PEANUTBUTTER=JELLY

And now here we add ``--cleanenv`` to see that it doesn’t.

::

    PEANUTBUTTER=JELLY singularity exec --cleanenv Centos7.img env | grep PEANUT

Here we will test contain. We can first confirm that there are a lot of files on our host in /tmp, and the same files are found in the container.

::

    # On the host
    $ ls /tmp | wc -l
    17

    # And then /tmp is mounted to the container, by default
    $ singularity exec Centos7.img  ls /tmp | wc -l

    # ..but not if we use --contain
    $ singularity exec --contain Centos7.img  ls /tmp | wc -l
    0
