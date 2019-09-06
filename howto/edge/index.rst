Managing EnOS Edge devices
=========================

You can use EnOS™ Edge to ingest data from on-site devices or 3rd party systems to the EnOS Cloud. As the front end of the EnOS Cloud, EnOS Edge extends the data ingestion capability through supporting various *communication protocol* and providing *device templates* to bridge the heterogeneous actual measuring points and the common device models.

Key Concepts
---------------

Device Template
``````````````````

A device template is the adapter between a common device model and a specific model of the device. A device template allows mapping between a specific device measuring point and a common device model.

Based on industrial experiences, EnOS has accumulated a number of device templates for reusing. You can also define customized device templates.

Communication Protocol
`````````````````````````

Communication protocol is a common phrase used in energy industry. EnOS Edge provides a rich protocol library that contains common protocols of energy and power industry, such as modbus, IEC104, OPCDA, OPCUA, and OPC-XML-DA. In addition, EnOS supports conventions of the major device manufacturers in the industry. For more information, see `Protocols Supported by EnOS Edge </docs/enos-edge/en/latest/learn/data_ingestion_normalization.html>`__ .

With these protocols, you can search and select desired protocols in the device template, which can be directly used after simple configuration  to avoid development or integration of protocols.

Related captions
-----------------

.. toctree::
   :maxdepth: 1

   managing_edge
   managing_template
   creating_protocol
