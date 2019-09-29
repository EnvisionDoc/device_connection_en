Offline Message Integration
=========================

Device Protocol for MQTT offline message defines the historical data format of the MQTT-based transmission between 3rd-party systems and EnOS cloud.

The supported MQTT version:

- MQTT v3.1.1 on port 11883 if you use the secret-based one-way authentication.
- MQTT v3.1.1 on port 18883 if you use the certificate-based two-way authentication.

.. toctree::
   :caption: Common Parameters
   :maxdepth: 1

   common_param_offline

.. toctree::
   :caption: Establishing Connection
   :maxdepth: 1

   nonsdk_login_offline

.. toctree::
   :caption: Upstream
   :maxdepth: 1

   report_offline_data_passthrough
   report_offline_events
   report_offline_points
   report_offline_attributes


