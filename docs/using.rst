====================
Using Anaconda Cloud
====================

Getting started
===============

Overview
~~~~~~~~

.. _installing-anaconda-client-and-anaconda-build:

Installing anaconda-client and anaconda-build
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These two tools work together. The Anaconda client command line
interface (CLI) is used to connect to and manage your Anaconda Cloud
account, upload packages you have created, and generate access tokens to
allow access to private packages. View the complete list of tasks after
installing with this command::

    anaconda -h

Anaconda build is used to control the Anaconda Cloud package building
system.

These tools can be installed in three ways: with conda, with PIP, or
with PIP from source. We recommend using conda.

Option 1, `conda <http://conda.pydata.org/>`__::

    conda install anaconda-client anaconda-build

Option 2, `PIP <https://pip.pypa.io/en/latest/>`__::

    pip install anaconda-client anaconda-build

Option 3, installing with pip from source::

    pip install git+https://github.com/Anaconda-Server/anaconda-client
    pip install git+https://github.com/Anaconda-Server/anaconda-build

Running anaconda-build workers on Linux requires installing the
prerequisite tools tar, bzip2, ntp, chrpath, wget, dos2unix, patch, gcc,
gcc-c++, git, and subversion::

    yum install -y tar bzip2 ntp chrpath wget dos2unix patch gcc gcc-c++ git subversion

.. _using-packages:

Packages
========

Namespaces
~~~~~~~~~~

Each user and organization has their own location called a **user
namespace** where they may host packages. You can view the public
packages in a user or organization's namespace by navigating to their
user page.

EXAMPLE: The `travis <https://anaconda.org/travis>`__ user namespace
located at https://anaconda.org/travis contains packages that were
uploaded and shared by the user whose account is named Travis.

NOTE: All packages are public if uploaded by users of free accounts.
Packages may be designated as private by upgrading to a paid account.

Anaconda Cloud supports two package managers,
:ref:`conda <using-conda-packages>` and
:ref:`PyPI <using-pypi-packages>`. To work with conda or PyPI packages,
you must use their corresponding subdomains:

-  To install **conda** packages from the conda user, use the repository
   url
   `https://\ **conda**.anaconda.org/conda <https://conda.anaconda.org/conda>`__
-  To install **pypi** packages from the conda user, use the repository
   url
   `https://\ **pypi**.anaconda.org/conda <https://pypi.anaconda.org/conda>`__


Labels
~~~~~~

Each file within a package may be tagged with one or more labels, or not
tagged at all. The use of labels allows package authors to upload files
for development or testing purposes without affecting non-development
users.

At the channel
`https://conda.anaconda.org/sean <https://conda.anaconda.org/sean>`__ a
dropdown menu lists all the available labels.

For example, all of the conda packages labeled as "dev" can be shown
here:

-  `https://conda.anaconda.org/sean/label/dev <https://conda.anaconda.org/sean/label/dev>`__

The default label is **main**, so packages that are uploaded without
specifying a label are automatically labeled "main". The version labeled
main is also delivered by default unless a user specifies a different
label. So, if a file is labeled as **main** then the label name may be
omitted from the URL. For example the following repositories are
equivalent:

-  `https://conda.anaconda.org/sean/label/main <https://conda.anaconda.org/sean/label/main>`__
-  `https://conda.anaconda.org/sean <https://conda.anaconda.org/sean>`__

Commands such as ``conda install`` can be used with a channel or used
with a channel and a label::

     conda install —-channel sean selenium
     conda install —-channel sean/label/dev selenium

.. _using-conda-packages:

Conda packages
~~~~~~~~~~~~~~

.. _uploading-conda-packages:

Uploading
^^^^^^^^^

This example shows how to build and upload a
`conda <http://conda.pydata.org/>`__ package to Anaconda Cloud.

If you do not already use conda, follow the miniconda `quick
install <http://conda.pydata.org/docs/install/quick.html>`__
instructions.

Now make sure you have Anaconda client and conda build installed::

      conda install anaconda-client conda-build

Now choose the repository you would like to build the package for. In
this example we'll use a simple `conda test
package <https://github.com/Anaconda-Server/anaconda-client/tree/master/example-packages/conda>`__::

      git clone https://github.com/Anaconda-Server/anaconda-client
      cd anaconda-client/example-packages/conda/

In this directory we can see two required files:
`meta.yaml <https://github.com/Anaconda-Server/anaconda-client/blob/master/example-packages/conda/meta.yaml>`__
and
`build.sh <https://github.com/Anaconda-Server/anaconda-client/blob/master/example-packages/conda/build.sh>`__
(for Linux or Mac) or bld.bat (for Windows). To build the package, turn
off automatic anaconda-client uploading and then run the conda build
command::

      conda config --set anaconda_upload no
      conda build .

All packages built in this way are placed in a subdirectory of
:doc:`Anaconda's </anaconda/index>` *conda-bld*
directory. You can check where the resulting file was placed with the
``--output`` option::

      conda build . --output

Now upload the test package to Anaconda Cloud with the :ref:`anaconda
upload <cli-upload>` command::

      anaconda login
      anaconda upload /path/to/conda-package.tar.bz2

You may also wish to read the articles `Building conda
packages <http://conda.pydata.org/docs/building/bpp.html>`__ and
`Tutorials on conda
build <http://conda.pydata.org/docs/build_tutorials.html>`__ for more
information on conda's overall build framework.


Installing
^^^^^^^^^^

Install conda packages from Anaconda Cloud by adding channels to your
conda config.

Conda knows how to interact with Anaconda Cloud. Specifying the channel
``sean`` translates to
`https://conda.anaconda.org/sean <https://conda.anaconda.org/sean>`__::

      conda config --add channels sean

Now you can install public conda packages from sean's Anaconda Cloud
account. Try installing the `testci
package <https://anaconda.org/sean/testci>`__::

      conda install testci

.. _using-pypi-packages:

PyPI packages
~~~~~~~~~~~~~

.. _uploading-pypi-packages:

Uploading PyPI packages
^^^^^^^^^^^^^^^^^^^^^^^

We can test PyPI package uploading with a small example package saved in
the `anaconda-client
repository <https://github.com/Anaconda-Server/anaconda-client/tree/master/example-packages/pypi>`__.
Begin by cloning the repository from the command line::

      git clone git@github.com:Anaconda-Server/conda-server.git
      cd conda-server/example-packages/pypi/

Now you can create your PyPI package with the ``setup.py`` script::

      python setup.py sdist

The package has now been built as a source tarball and is ready to be
uploaded::

      anaconda upload dist/*.tar.gz

Your package is now available at
``http://anaconda.org/USERNAME/PACKAGE``.


Installing PyPI packages
^^^^^^^^^^^^^^^^^^^^^^^^

The best way to install a PyPI package is using pip. For the following,
we will use the package we authored in the examples above::

      pip install --extra-index-url https://pypi.anaconda.org/USERNAME/simple pypi-test-package


Installing private PyPI packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All Anaconda Cloud urls can be prefixed with ``/t/$TOKEN`` to access
private packages::

      TOKEN=$(anaconda auth --create --name YOUR-TOKEN-NAME)
      pip install --index-url https://pypi.anaconda.org/t/$TOKEN/USERNAME/simple test-package

.. _using-private-packages:

Private packages
~~~~~~~~~~~~~~~~

Packages may be private. This means that a user must explicitly have
access to view the package. To view and install private packages, you
must identify yourself to Anaconda Cloud. This is done with :ref:`access
tokens <using-tokens>`. Once you have generated a token
(``<TOKEN>``), you may prefix any repository url with ``/t/<TOKEN>``

Note: This is just an example. You will not see any extra private
packages in the travis **user namespace**.

-  To install **private conda** packages from the user travis, use the
   repository url
   `https://\ **conda**.anaconda.org/t/<TOKEN>/travis <https://conda.anaconda.org/travis>`__
-  To install **private pypi** packages from the user travis, use the
   repository url
   `https://\ **pypi**.anaconda.org/t/<TOKEN>/travis <https://pypi.anaconda.org/travis>`__

.. _using-tokens:

Tokens
~~~~~~

You can use tokens to control access to private repositories,
collections, or packages on Anaconda Cloud. Additionally, the degree of
access a token grants is completely configurable at the time of
generation.


Generating tokens
^^^^^^^^^^^^^^^^^

Tokens are generated with the Anaconda client::

      anaconda auth --create --name YOUR-TOKEN-NAME --scopes 'repos conda:download'

This generates a random alphanumeric token string, which you can then
distribute to fellow Anaconda Cloud users to enable them to download a
package that you have marked private. The token produced in this example
provides access to download any of your private conda repositories. It
can be enabled with the ``conda config`` command::

      conda config --add channels https://conda.anaconda.org/t/TOKEN/USERNAME


Package privacy settings
~~~~~~~~~~~~~~~~~~~~~~~~

You will be prompted with two options:

#. **Personal**: The new package will be hosted on your personal
   repository. This package will be viewable and installable by
   anonymous users. Users must add your unique repository url to their
   package manager's configuration.
#. **Private**: The new package will be hosted on your personal
   repository; however, you control the list of authorized users that
   will be able to access or modify this package.


Uploading packages
~~~~~~~~~~~~~~~~~~

To easily upload package files to Anaconda Cloud use the
:doc:`anaconda-client <cli>` command line interface and the
:ref:`upload <cli-upload>` command::

      anaconda login
      anaconda upload PACKAGENAME

Anaconda Cloud automatically detects packages and notebooks, package or
notebook types, and their versions.

Your package is now available at:
``https://anaconda.org/<USERNAME>/<PACKAGENAME>``

Your package can be also downloaded by anyone using the Anaconda CLI::

      anaconda download USERNAME/PACKAGENAME


Uploading other types of files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In addition to uploading packages, you can also upload other types of
files to Anaconda Cloud. In this example we will upload a spreadsheet
named baby-names in comma separated value (CSV) format. Any type of file
can be uploaded with the Anaconda CLI by using these steps.

#. Use the :doc:`anaconda-client <cli>` command line interface to
   create a new namespace for your file on Anaconda Cloud::

       anaconda login
       anaconda package --create USERNAME/baby-names

#. Now you can upload the file to your new namespace. Unlike uploading
   packages or notebooks, there is no auto-detect for other types of
   files. You must explicitly specify the 'package', 'package-type' and
   'version' fields.

   In this example the package name is baby-names, the package type is a
   file, this is the first version that we are uploading, and the full
   filename is baby-names1.csv::

       anaconda upload --package baby-names --package-type file --version 1 baby-names1.csv


Downloading other types of files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Your file is available at
``https://anaconda.org/<USERNAME>/<babynames>``

Your file can also be downloaded by anyone using the Anaconda CLI::

        anaconda download USERNAME/baby-names


Remove a past version of a package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To remove a past version of one of your packages from Anaconda Cloud:

#. Click the package name.

#. Click the tab "Files".

#. Click the checkbox to the left of the version you wish to remove.

#. Click the "Actions" menu and then "Remove".

You may instead use the :doc:`command line interface <cli>`::

      anaconda remove jsmith/testpack/0.2

NOTE: Replace ``jsmith``, ``testpack``, and ``0.2`` with your actual
user name, package name, and version.

The change can now be seen on your profile page:
``https://anaconda.org/<USERNAME>/<PACKAGE>``


Delete a package
~~~~~~~~~~~~~~~~

To delete one of your packages from Anaconda Cloud, including all of its
versions:

#. Click the package name.

#. Click the tab "Settings".

#. Click "Admin" on the left side menu.

#. Click "Delete".

You may instead use the :doc:`command line interface <cli>`::

      anaconda remove jsmith/testpak

NOTE: Replace ``jsmith`` and ``testpak`` with your actual user name and
package name.

The change can now be seen on your profile page:
``https://anaconda.org/<USERNAME>``


Notebooks
=========

Upload a `Jupyter notebook <http://jupyter.org/>`__ (formerly IPython
notebook) to Anaconda Cloud::

    anaconda upload my-notebook.ipynb

An HTML version of the notebook will be at::

    http://notebooks.anaconda.org/<USERNAME>/my-notebook

Anyone can download it::

    anaconda download username/my-notebook


Environments
============

Save a `conda
environment <http://conda.pydata.org/docs/using/envs.html>`__ and upload
it to Anaconda Cloud::

    conda env export -n my-environment
    conda env upload my-environment

A list of your uploaded environments is at::

    http://envs.anaconda.org/<USERNAME>

Anyone can download and install your environment from Anaconda Cloud::

    conda env create user/my-environemnt
    source activate my-environment


Organizations
=============

Organizations enable you to maintain group-owned repositories.

.. _creating-orgs:

Creating organizations
~~~~~~~~~~~~~~~~~~~~~~

To create organizations, click the grid icon at the top of the page,
select "Organizations", and use the form at the bottom of that page.


Managing organizations
~~~~~~~~~~~~~~~~~~~~~~

You can view your organizations by navigating to "Group Membership" 
which lists all of the organizations/groups to which you belong::

    https://anaconda.org/<USERNAME>/groups

Or by navigating to `anaconda.org <https://anaconda.org>`__ and
selecting the organization dropdown on the upper right.

You can manage your organization's settings by navigating to::

    https://anaconda.org/organization/ORGANIZATION/settings/profile

Or by navigating to `your settings <https://anaconda.org/settings>`__
and selecting the organization dropdown on the upper right.


Adding another owner to your organization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

All organization owners have full access to the organization settings
and all packages.

To give other users ownership, navigate to the groups settings page,
choose "owners", type their names into the text box, and choose "add"::

    https://anaconda.org/organization/ORGANIZATION/settings/groups

|Org groups page|

|Org owners page|


Uploading packages to an organization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To upload a package to an organization, use the ``-u/--user`` option::

    anaconda upload --user USERNAME package.tar.bz2

.. _using-labels-in-the-development-cycle:

Using labels in the development cycle
=====================================

Labels can be used to facilitate a development cycle and organize the
code that is in development, in testing, and in production.

Anacona Cloud labels allow you to upload files to your packages and
control how they are accessed.

With Anaconda Cloud labels you can upload a file to a specific label, so
only users who put that label in the URL they search will be able to
install it. This is particularly useful for moving a package through a
development and testing flow.

In this example we will show you how to use a ``test`` label, so that
you can upload files without affecting your production quality packages.
Without a ``--label`` argument the default label is ``main``.

Let's start with a conda package. If you don't have one, use our example
conda package. Before you build the package edit the version in the
``meta.yaml`` file in ``anaconda-client/example-packages/conda/`` to be
2.0.

::

    git clone https://github.com/Anaconda-Server/anaconda-client
    cd anaconda-client/example-packages/conda/
    vim meta.yaml # Bump version to 2.0
    conda config --set anaconda_upload no
    conda build .

Now, upload your test package to Anaconda Cloud using the
:ref:`anaconda-client upload <cli-upload>` command.

Adding the ``--label`` option tells Anaconda Cloud to make the upload
visible only to users who specify that label::

    anaconda upload /path/to/conda-package-2.0.tar.bz2 --label test

You will notice now that even when you search conda ``main``, you won't
see the ``2.0`` version of the test package. This is because you have to
tell conda to look for your new ``test`` label.

The ``--override`` argument tells conda not to use any channels in your
``~/.condarc`` file.

No 2.0 results::

    conda search --override -c USERNAME conda-package

Your 2.0 package is here::

    conda search --override -c USERNAME/label/test conda-package

You can give the label ``USERNAME/label/test`` to your testers. Once
they finish testing, you may then want to copy the ``test`` packages
back to your ``main`` label.

You can also manage your package labels from your dashboard:
``https://anaconda.org/USERNAME/conda-package``

::

    anaconda label --copy test main

Now your version 2.0 is in main::

    conda search --override -c USERNAME conda-package

.. |Org groups page| image:: /img/cloud-org-groups.png
.. |Org owners page| image:: /img/cloud-org-owners.png
