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
