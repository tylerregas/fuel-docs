.. index:: Object storage, Swift

.. _Swift-and-object-storage-notes:

Object storage deployment notes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fuel currently supports several scenarios to deploy the object storage:

* Glance + filesystem

  By default, Glance uses the file system backend to store virtual machine images. 
  In this case, you can use any of shared file systems supported by Glance. 

* Swift on controllers

  In this mode the role of swift-storage and swift-proxy are combined with a 
  nova-controller. Use it only for testing in order to save nodes. It's not 
  suitable for production environments.

* Swift on dedicated nodes

  In this case the Proxy service and Storage (account/container/object) services 
  reside on separate nodes, with two proxy nodes and a minimum of three storage 
  nodes.

Now let's look at performing an actual OpenStack deployment using Fuel.

