Real-time Data Integration
=========================

Device Protocol for MQTT real-time data defines the data format of the MQTT-based transmission between devices and EnOS cloud.

The supported MQTT version:

- MQTT v3.1.1 on port 11883 if you use the secret-based one-way authentication.
- MQTT v3.1.1 over SSL/TLS on port 18883 if you use the certificate-based two-way authentication.

**Prerequisites**

Complete the cloud configuration in the EnOS Console and obtain the following information that are needed in the device-end ddevelopment:

- Device credentials for authentication
- Topics to subscribe data from and publish data to


**In This Section**

.. toctree::
   :caption: Common Parameters
   :maxdepth: 1

   common_param

.. toctree::
   :caption: Establishing Connection
   :maxdepth: 1

   nonsdk_login

.. toctree::
   :caption: Upstream
   :maxdepth: 1

   upstream/index


.. toctree::
   :caption: Downstream
   :maxdepth: 1

   downstream/index

.. toctree::
   :maxdepth: 2
   :caption: Topology managament

   topology_management/index
