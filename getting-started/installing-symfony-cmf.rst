Installing the Symfony CMF Standard Edition
===========================================

The goal of this tutorial is to install all the CMF components with the minimum necessary
configuration and some very simple examples into a working Symfony2 application. This can
be used to familiarize yourself with the CMF or to be used as a starting point for a new
custom application.

If this is your first encounter with the Symfony CMF it would be a good idea to first take a
look at:

- `The Big Picture <http://slides.liip.ch/static/2012-01-17_symfony_cmf_big_picture.html#1>`_
- The online sandbox demo at `cmf.liip.ch <http://cmf.liip.ch>`_

.. note::

    For other Symfony CMF installation guides, please read:
    - The cookbook entry on :doc:`../cookbook/installing-cmf-sandbox` for instructions on how to install a more complete demo instance of Symfony CMF.
    - :doc:`../tutorials/installing-cmf-core` for step-by-step installation and configuration details of just the core components into an existing Symfony application.

.. index:: Standard Edition, install

Preconditions
-------------

As Symfony CMF is based on Symfony2, you should make sure you meet the
`Requirements for running Symfony2 <http://symfony.com/doc/current/reference/requirements.html>`_.
Additionally, you need to have `SQLite <http://www.sqlite.org/>`_ PDO extension (pdo_sqlite)
installed, since it is used as the default storage medium.

.. note::

    By default, Symfony CMF uses Jackalope + Doctrine DBAL, and SQLite as
    the underlying DB. However, Symfony CMF is storage agnostic, which means
    you can use one of several available data storage mechanisms without
    having to rewrite your code. For more information on the different
    available mechanisms and how to install and configure them, refer to
    :doc:`../tutorials/installing-configuring-doctrine-phpcr-odm`

`Git <http://git-scm.com/>`_ and `Curl <http://curl.haxx.se/>`_ are also needed to follow the installation steps listed below.


Installation
------------

The easiest way to install Symfony CMF is is using `Composer <http://getcomposer.org/>`_.
Get it using

.. code-block:: bash

    curl -s http://getcomposer.org/installer | php --

and then get the Symfony CMF code with it (this may take a while)

.. code-block:: bash

    php composer.phar create-project symfony-cmf/standard-edition <path-to-install> --stability=dev
    mv composer.phar <path-to-install>/.
    cd <path-to-install>

.. note::

    It is actually recommended to move ``composer.phar`` into the bin directory of your filesystem,
    so that you can access the command from any directory.

The path ``<path-to-install>`` should either inside your web server doc root or configure
a virtual host for ``<path-to-install>``.

This will clone the standard edition and install all the dependencies and run some initial commands.
These commands require write permissions to the ``app/cache`` and ``app/logs`` directory. In case
the final commands end up giving permissions errors, please follow the `guidelines in the official
documentation <http://symfony.com/doc/master/book/installation.html#configuration-and-setup>`_ for
configuring the permissions and then run the ``composer.phar install`` command mentioned below.

If you prefer you can also just clone the project:

.. code-block:: bash

    git clone git://github.com/symfony-cmf/symfony-cmf-standard.git <dir-name>
    cd <dir-name>

If there were problems during the ``create-project`` command, or if you used ``git clone`` or if you
updated the checkout later, always run the following command to update the dependencies:

.. code-block:: bash

    php composer.phar install

The next step is to setup the database, if you want to use SQLite as your database backend just go ahead and run the following:

.. code-block:: bash

    app/console doctrine:database:create
    app/console doctrine:phpcr:init:dbal
    app/console doctrine:phpcr:register-system-node-types
    app/console doctrine:phpcr:fixtures:load

This will create a file called app.sqlite inside your app folder, containing the database content.

The project should now be accessible on your web server. If you have PHP 5.4 installed
you can alternatively use the PHP internal web server:

.. code-block:: bash

    app/console server:run

And then access the CMF via:

.. code-block:: text

    http://localhost:8000

If you prefer to use another database backend, for example MySQL, run the configurator (point your browser
to ``/web/config.php``) or set your database connection parameters in ``app/config/parameters.yml``. Make sure you
leave the ``database_path`` property at ``null`` in order to use another driver than SQLite. Leaving the field blank
in the web-configurator should set it to ``null``.

Overview
--------

This guide will help you understand the basic parts of Symfony CMF Standard
Edition (SE) and how they work together to provide the default pages you
can see when browsing the Symfony CMF SE installation.

It assumes you have already installed Symfony CMF SE and have carefully
read the Symfony2 book.

.. note::

    For other Symfony CMF installation guides, please read:
    - The cookbook entry on :doc:`../cookbook/installing-cmf-sandbox` for instructions on how to install a more complete demo instance of Symfony CMF.
    - :doc:`../tutorials/installing-cmf-core` for step-by-step installation and configuration details of just the core components into an existing Symfony application.

AcmeMainBundle and SimpleCMSBundle
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Symfony CMF SE comes with a default AcmeMainBundle to help you get started,
in a similar way that Symfony2 has AcmeDemoBundle, providing you some
demo pages visible on your browser. However, AcmeMainBundle doesn't include
controllers or configuration files, like you probably would expect. It contains
little more than a twig file and `Fixtures <http://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html>`_
data, that was loaded into your database during installation.

There are several bundles working together in order to turn the fixture data
into a browsable website. The overall, simplified process is:

- When a request is received, the Symfony CMF :doc:`routing`'s Dynamic Router is used to handle the incoming request.
- The Dynamic Router is able to match the requested URL with a specific ContentBundle's Content stored in the database.
- The retrieved content's information is used to determine which controller to pass it on to, and which template to use.
- As configured, the retrieved content is passed to ContentBundle's ContentController, which will handle it and render AcmeMainBundle's layout.html.twig.

 Again, this is simplified view of a very simple CMS built on top of Symfony CMF.
 To fully understand all the possibilities of the CMF, a carefull look into
 each component is needed.