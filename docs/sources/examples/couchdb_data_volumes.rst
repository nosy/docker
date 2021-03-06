:title: Sharing data between 2 couchdb databases
:description: Sharing data between 2 couchdb databases
:keywords: docker, example, package installation, networking, couchdb, data volumes

.. _running_couchdb_service:

CouchDB 服务
===============

.. include:: example_header.inc

Here's an example of using data volumes to share the same data between
two CouchDB containers.  This could be used for hot upgrades, testing
different versions of CouchDB on the same data, etc.

创建第一个数据库
---------------------

Note that we're marking ``/var/lib/couchdb`` as a data volume.

.. code-block:: bash

    COUCH1=$(sudo docker run -d -p 5984 -v /var/lib/couchdb shykes/couchdb:2013-05-03)

添加数据到第一个数据库
------------------------------

We're assuming your Docker host is reachable at ``localhost``. If not,
replace ``localhost`` with the public IP of your Docker host.

.. code-block:: bash

    HOST=localhost
    URL="http://$HOST:$(sudo docker port $COUCH1 5984 | grep -Po '\d+$')/_utils/"
    echo "Navigate to $URL in your browser, and use the couch interface to add data"

创建第二个数据库
----------------------

This time, we're requesting shared access to ``$COUCH1``'s volumes.

.. code-block:: bash

    COUCH2=$(sudo docker run -d -p 5984 --volumes-from $COUCH1 shykes/couchdb:2013-05-03)

在第二个数据库中浏览数据
----------------------------------

.. code-block:: bash

    HOST=localhost
    URL="http://$HOST:$(sudo docker port $COUCH2 5984 | grep -Po '\d+$')/_utils/"
    echo "Navigate to $URL in your browser. You should see the same data as in the first database"'!'

Congratulations, you are now running two Couchdb containers, completely
isolated from each other *except* for their data.
