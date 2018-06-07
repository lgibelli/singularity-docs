===================
Image Command Group
===================

------------
image.export
------------

.. _sec:imageexport:

| Export is a way to dump the contents of your container into a ``.tar.gz``, or a
  stream to put into some other place. For example, you could stream
  this into an in memory tar in python. Importantly, this command was
  originally intended for Singularity version less than 2.4 in the case
  of exporting an ext3 filesystem. For Singularity greater than 2.4, the
  resulting export file is likely to be larger than the original
  squashfs counterpart. An example with an ext3 image is provided.
| Here we export an image into a ``.tar`` file:

::

    singularity image.export container.img > container.tar

We can also specify the file with ``--file``

::

    singularity image.export --file container.tar container.img

And here is the recommended way to compress your image:

::

    singularity image.export container.img | gzip -9 > container.img.tar.gz

------------
image.expand
------------

.. _sec:imageexpand:

While the squashfs filesystem means that you typically don’t need to
worry about the size of your container being built, you might find that
if you are building an ext3 image (pre Singularity 2.4) you want to
expand it.

Increasing the size of an existing image
========================================

| You can increase the size of an image after it has been instantiated
  by using the image.expand Singularity sub-command. In the example
  below, we:

#. create an empty image

#. inspect it’s size

#. expand it

#. confirm it’s larger

::

    $ singularity image.create container.img
    Creating empty 768MiB image file: container.imglarity image.create container.im
    Formatting image with ext3 file system
    Image is done: container.img

    $ ls -lh container.img
    -rw-rw-r-- 1 vanessa vanessa 768M Oct  2 18:48 container.img

    $ singularity image.expand container.img
    Expanding image by 768MB
    Checking image's file system
    e2fsck 1.42.13 (17-May-2015)
    Pass 1: Checking inodes, blocks, and sizes
    Pass 2: Checking directory structure
    Pass 3: Checking directory connectivity
    Pass 4: Checking reference counts
    Pass 5: Checking group summary information
    container.img: 11/49152 files (0.0% non-contiguous), 7387/196608 blocks
    Resizing image's file system
    resize2fs 1.42.13 (17-May-2015)
    Resizing the filesystem on container.img to 393216 (4k) blocks.
    The filesystem on container.img is now 393216 (4k) blocks long.
    Image is done: container.img

    $ ls -lh container.img
    -rw-rw-r-- 1 vanessa vanessa 1.5G Oct  2 18:48 container.img

Similar to the create sub-command, you can override the default size
increase (which is 768MiB) by using the ``--size`` option.

------------
image.import
------------

.. _sec:imageimport:

| Singularity import is essentially taking a dump of files and folders
  and adding them to your image. This works for local compressed things
  (e.g., tar.gz) but also for docker image layers that you don’t have on
  your system. As of version 2.3, import of docker layers includes the
  environment and metadata without needing sudo. It’s generally very
  intuitive.
| As an example, here is a common use case: wanting to import a Docker
  image:

::

    singularity image.import container.img docker://ubuntu:latest

------------
image.create
------------

.. _sec:imagecreate:

| A Singularity image, which can be referred to as a “container,” is a
  single file that contains a virtual file system. As of Singularity
  2.4, we strongly recommend that you build (create and install) an
  image using `build <#build-a-container>`_. If you have reason to create an empty image, or use
  create for any other reason, the original ``create`` command is replaced with a
  more specific ``image.create``. After creating an image you can install an operating
  system, applications, and save meta-data with it.
| Whereas Docker assembles images from layers that are stored on your
  computer (viewed with the ``docker history`` command), a Singularity image is just one
  file that can sit on your Desktop, in a folder on your cluster, or
  anywhere. Having Singularity containers housed within a single image
  file greatly simplifies management tasks such as sharing, copying, and
  branching your containers. It also means that standard Linux file
  system concepts like permissions, ownership, and ACLs apply to the
  container (e.g. I can give read only access to a colleague, or block
  access completely with a simple ``chmod`` command).

Creating a new blank Singularity container image
================================================

Singularity will create a default container image of 768MiB using the
following command:

::

    singularity image.create container.img
    Creating empty 768MiB image file: container.img
    Formatting image with ext3 file system
    Image is done: container.img

| How big is it?

::

    $ du -sh container.img
    29M     container.img

Create will make an ``ext3`` filesystem. Let’s create and import a docker base
(the pre-2.4 way with two commands), and then compare to just building
(one command) from the same base.

::

    singularity create container.img
    sudo singularity bootstrap container.img docker://ubuntu

    ...

    $ du -sh container.img
    769M

Prior to 2.4, you would need to provide a ``--size`` to change from the default:

::

    $ singularity create --size 2048 container2.img
    Initializing Singularity image subsystem
    Opening image file: container2.img
    Creating 2048MiB image
    Binding image to loop
    Creating file system within image
    Image is done: container2.img

    $ ls -lh container*.img
    -rwxr-xr-x 1 user group 2.1G Apr 15 11:34 container2.img
    -rwxr-xr-x 1 user group 769M Apr 15 11:11 container.img

Now let’s compare to if we just built, without needing to specify a
size.

::

    sudo singularity build container.simg docker://ubuntu

    ...

    du -sh container.simg
    45M container.simg

Quite a difference! And one command instead of one.

Overwriting an image with a new one
-----------------------------------

For any commands that If you have already created an image and wish to
overwrite it, you can do so with the ``--force`` option.

::

    $ singularity image.create container.img
    ERROR: Image file exists, not overwriting.


    $ singularity image.create --force container.img
    Creating empty 768MiB image file: container.img
    Formatting image with ext3 file system
    Image is done: container.img

``@GodLoveD`` has provided a nice interactive demonstration of creating an image (pre
2.4).


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
