==========
Deprecated
==========

    Note: The bootstrap command is deprecated for Singularity Version
    2.4. You should use `build <http://singularity-userdoc.readthedocs.io/en/latest/getting_started.html#build-a-container>`_ instead.

---------
bootstrap
---------

.. _sec:bootstrap:

Bootstrapping was the original way (for Singularity versions prior to
2.4) to install an operating system and then configure it appropriately
for a specified need. Bootstrap is very similar to build, except that it
by default uses an `ext3`_ filesystem and allows for writability. The
images unfortunately are not immutable in this way, and can degrade over
time. As of 2.4, bootstrap is still supported for Singularity, however
we encourage you to use `build <#build-a-container>`_ instead.

Quick Start
===========

| A bootstrap is done based on a Singularity recipe file (a text file
  called Singularity) that describes how to specifically build the
  container. Here we will overview the sections, best practices, and a
  quick example.

::

    $ singularity bootstrap
    USAGE: singularity [...] bootstrap <container path> <definition file>

| The ``<container path>`` is the path to the Singularity image file, and the ``<definition file>`` is the location
  of the definition file (the recipe) we will use to create this
  container. The process of building a container should always be done
  by root so that the correct file ownership and permissions are
  maintained. Also, so installation programs check to ensure they are
  the root user before proceeding. The bootstrap process may take
  anywhere from one minute to one hour depending on what needs to be
  done and how fast your network connection is.
| Let’s continue with our quick start example. Here is your spec file, ``Singularity`` ,

::

    Bootstrap:docker
    From:ubuntu:latest

| You next create an image:

::

    $ singularity image.create ubuntu.img
    Initializing Singularity image subsystem
    Opening image file: ubuntu.img
    Creating 768MiB image
    Binding image to loop
    Creating file system within image
    Image is done: ubuntu.img

and finally run the bootstrap command, pointing to your image ( ``<container path>`` ) and
the file Singularity ( ``<definition file>`` ).

::

    $ sudo singularity bootstrap ubuntu.img Singularity
    Sanitizing environment
    Building from bootstrap definition recipe
    Adding base Singularity environment to container
    Docker image path: index.docker.io/library/ubuntu:latest
    Cache folder set to /root/.singularity/docker
    [5/5] |===================================| 100.0%
    Exploding layer: sha256:b6f892c0043b37bd1834a4a1b7d68fe6421c6acbc7e7e63a4527e1d379f92c1b.tar.gz
    Exploding layer: sha256:55010f332b047687e081a9639fac04918552c144bc2da4edb3422ce8efcc1fb1.tar.gz
    Exploding layer: sha256:2955fb827c947b782af190a759805d229cfebc75978dba2d01b4a59e6a333845.tar.gz
    Exploding layer: sha256:3deef3fcbd3072b45771bd0d192d4e5ff2b7310b99ea92bce062e01097953505.tar.gz
    Exploding layer: sha256:cf9722e506aada1109f5c00a9ba542a81c9e109606c01c81f5991b1f93de7b66.tar.gz
    Exploding layer: sha256:fe44851d529f465f9aa107b32351c8a0a722fc0619a2a7c22b058084fac068a4.tar.gz
    Finalizing Singularity container

Notice that bootstrap does require sudo. If you do an import, with a
docker uri for example, you would see a similar flow, but the calling
user would be you, and the cache your ``$HOME``.

::

    $ singularity image.create ubuntu.img
    singularity import ubuntu.img docker://ubuntu:latest
    Docker image path: index.docker.io/library/ubuntu:latest
    Cache folder set to /home/vanessa/.singularity/docker
    Importing: base Singularity environment
    Importing: /home/vanessa/.singularity/docker/sha256:b6f892c0043b37bd1834a4a1b7d68fe6421c6acbc7e7e63a4527e1d379f92c1b.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:55010f332b047687e081a9639fac04918552c144bc2da4edb3422ce8efcc1fb1.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:2955fb827c947b782af190a759805d229cfebc75978dba2d01b4a59e6a333845.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:3deef3fcbd3072b45771bd0d192d4e5ff2b7310b99ea92bce062e01097953505.tar.gz
    Importing: /home/vanessa/.singularity/docker/sha256:cf9722e506aada1109f5c00a9ba542a81c9e109606c01c81f5991b1f93de7b66.tar.gz
    Importing: /home/vanessa/.singularity/metadata/sha256:fe44851d529f465f9aa107b32351c8a0a722fc0619a2a7c22b058084fac068a4.tar.gz

For details and best practices for creating your Singularity recipe, `read about them here <http://singularity-userdoc.readthedocs.io/en/latest/getting_started.html#container-recipes>`_.


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
