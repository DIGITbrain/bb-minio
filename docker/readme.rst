================
MinIO
================

About
=====

**MinIO** [1]_ MinIO is a high performance, distributed object storage system. It is software-defined, runs on industry standard hardware and is 100% open source with the dominant license being GNU AGPL v3. MinIO is different in that it was designed from its inception to be the standard in private/hybrid cloud object storage. Because MinIO is purpose-built to serve only objects, a single-layer architecture achieves all of the necessary functionality without compromise. The result is a cloud-native object server that is simultaneously performant, scalable and lightweight. While MinIO excels at traditional object storage use cases like secondary storage, disaster recovery and archiving, it is unique at overcoming the challenges associated with machine learning, analytics and cloud-native application workloads.

Version
-------
MinIO **Community** version **RELEASE.2022-03-17T06-34-49Z** 
available in Docker Hub [2]_ is deployed, 
which is created, maintained and supported by the MinIO team. 

License
-------
**GNU AGPLv3** [3]_

Pre-requisites
==============

* *docker* installed
* access to DIGITbrain private docker repo (username, password) to pull the image:
  
  - ``docker login dbs-container-repo.emgora.eu``
  - ``docker pull dbs-container-repo.emgora.eu/minio:8.3.3``

Usage
=====

.. code-block:: bash

  docker run -d --rm --name minio \
    -e MINIO_ROOT_USER=mydatabase \
    -e MINIO_ROOT_PASSWORD=myuser \
    -e MINIO_SERVER_URL=https://<public_ip>:10000 \
    -p 10000:9000 \
    -p 10001:9001 \
    minio:RELEASE.2022-03-17T06-34-49Z

where MINIO_ROOT_USER and MINIO_ROOT_PASSWORD are the access key and secret key to access the storage,
**MinIO Console** (GUI) is avaiable on port 10001 (container port: 9001),
**MinIO API** is avaiable on port 10000 (container port: 9000). Since MinIO Console
connects to MinIO API, MINIO_SERVER_URL gives the API URL to the Console.
This is necessary using custom port or server certificate containing the public IP (SAN),
and so loopback (localhost) connection will fail.

.. warning::
  Always update the ``MINIO_ROOT_USER`` and ``MINIO_ROOT_PASSWORD`` with values of your choice
  prior to running this container.


Security
========
The image uses access-key/secret-key and a server cerificate signed by DIGITbrain CA.

Note that as you cannot turn off hostname verification in the Console, so
you must use a server certificate issued for the particular <public_ip> and specify MINIO_SERVER_URL.
Certificates in the container can be found in directory: /root/.minio/certs/ and /root/.minio/certs/CAs/.
In order to use self-signed certificates, concatenate server certificate and the 
CA certificate (``cat {server.crt,ca.crt} > public.crt``) and place into /root/.minio/certs/CAs directory.

Using MinIO client, you can disable hostname verification.

.. code-block:: bash

  $ curl -o mc  https://dl.min.io/client/mc/release/linux-amd64/mc; chmod +x mc
  $ ./mc config --insecure host add myminio https://localhost:9000 username password
  $ ./mc admin trace --all --verbose myminio


Configuration
-------------

Environment variables
---------------------
.. list-table:: 
   :header-rows: 1

   * - Name
     - Example
     - Comment
   * - *MINIO_ROOT_USER*
     - ``-e MINIO_ROOT_USER=myaccesskey``
     - Root access key for MinIO
   * - *MINIO_ROOT_PASSWORD*
     - ``-e MINIO_ROOT_PASSWORD=mysecretkey``
     - Root secret kety for MinIO
   * - *MINIO_SERVER_URL*
     - ``-e MINIO_SERVER_URL=https://<public_ip>:10000``
     - URL for the API endpoint. You must use it to avoid invalid host IP, when using Console.
  
Ports
-----
.. list-table:: 
  :header-rows: 1

  * - Container port
    - Host port bind example
    - Comment
  * - 9000
    - ``-p 10000:9000``
    - Default MinIO API port 9000 is opened as port HTTPS 10000 on the host.
  * - 9001
    - ``-p 10001:9001``
    - Default MinIO Console port 9001 is opened as port HTTPS 10001 on the host.


Volumes
-------

The container might use the following volume mounts.

.. list-table:: 
   :header-rows: 1

   * - Name
     - Volume mount
     - Comment
   * - *data*    
     - -v $PWD/data_directory_on_host:/data  
     - MinIO data. Use this directory to persist data (will survive container restarts).
   * - *Server certificates*    
     - -v $PWD/certs:/root/.minio/certs  
     - Server key and certificate for API and Console with names: private.key and server.crt.
   * - *CA certificate*    
     - -v $PWD/certs/ca.pem:/root/.minio/certs/CAs/public.crt  
     - Certificate Authority certificate (containing server certificate and CA certificate too).
   
References
==========

.. [1] https://min.io/

.. [2] https://hub.docker.com/r/minio/minio

.. [3] https://min.io/pricing
