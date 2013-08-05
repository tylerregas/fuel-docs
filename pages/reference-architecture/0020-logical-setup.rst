
Logical Setup 
^^^^^^^^^^^^^

.. contents:: :local:

An OpenStack HA cluster involves, at a minimum, three types of nodes:
controller nodes, compute nodes, and storage nodes.

Controller Nodes
++++++++++++++++

The first order of business in achieving high availability (HA) is 
redundancy, so the first step is to provide multiple controller nodes. You 
must keep in mind, however, that the database uses Galera to achieve HA, and 
Galera is a quorum-based system. As such, you must provide a minimum of three 
controller nodes.

.. fancybox:: /_images/030-logical-diagram-controllers_svg.png
    :width: 400px
    :height: 400px

Every OpenStack controller runs keepalived that manages a single Virtual IP 
(VIP) for all controller nodes, and HAProxy that manages HTTP and TCP load 
balancing of requests going to OpenStack API services, RabbitMQ, and MySQL.

When an end user accesses the OpenStack cloud using Horizon or makes a 
request to the REST API for services such as nova-api, glance-api, 
keystone-api, quantum-api, nova-scheduler, MySQL or RabbitMQ, the request 
goes to the live controller node currently holding the VIP, and the 
connection gets terminated by HAProxy. When the next request comes in, 
HAProxy handles it, and may send it to the original controller or another in 
the cluster, depending on load conditions.

Each of the services housed on the controller nodes has its own
mechanism for achieving HA:

* nova-api, glance-api, keystone-api, quantum-api and nova-scheduler are 
  stateless services that do not require any special attention besides load 
  balancing.
* Horizon, as a typical web application, requires sticky sessions to be enabled 
  at the load balancer.
* RabbitMQ provides active/active high availability using mirrored queues.
* MySQL high availability is achieved through Galera active/active multi-master 
  deployment.

Compute Nodes
+++++++++++++

OpenStack compute nodes are, in many ways, the foundation of your cluster. 
Compute nodes are the servers on which your users will create their Virtual 
Machines (VMs) and host their applications. Compute nodes need to talk to 
controller nodes and reach out to essential services such as RabbitMQ and 
MySQL. They use the same approach that provides redundancy to the end-users 
of Horizon and REST APIs, reaching out to controller nodes using the VIP and 
going through HAProxy.

.. fancybox:: /_images/040-logical-diagram-compute_svg.png
    :width: 400px
    :height: 350px

Storage Nodes
+++++++++++++

In this OpenStack cluster reference architecture, shared storage acts as a 
backend for Glance, so that multiple Glance instances running on controller 
nodes can store images and retrieve images from it. To achieve this, you are 
going to deploy Swift. This enables you to use it not only for storing VM 
images, but also for any other objects such as user files.

.. fancybox:: /_images/050-logical-diagram-storage_svg.png
    :width: 400px
    :height: 400px

.. note:: Keep in mind that Swift manages data blobs in a flat file storage 
architecture and is not always suitable for all file storage needs.
