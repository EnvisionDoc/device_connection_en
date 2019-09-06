## Creating a protocol

### Procedure

Click **Create Protocol** on the home page of the Protocol Center. In the pop-up dialog box, select and fill in information related to the new protocol, and upload the protocol jar package.At the same time, you can upload a document about the specification of the protocol.As shown in the following figure:

.. image:: media/create_protocol.png

Parameter description:

- **Type** (required): Click the drop-down menu for protocol types, which displays some common protocol types that are predefined, such as Modbus RTU, Modbus TCP, DL645/97, DL645/07, IEC104, etc. The developer selects the type as needed based on the new protocol to be created.
- **Role** (required): Here role refers to the role of EnOS™ Edge at TCP layer and is selected by the developer depending on actual situations. Client refers to EnOS™ Edge as the TCP client; Server refers to EnOS™ Edge as the TCP server.
- **Name** (required): Move the mouse to.. image:: media/namebutton.png will display the naming convention. The complete protocol name will consist of “Protocol Type - Role –Name”. For a standard protocol, please use the default Std as the name. For example, if the protocol type is Modbus TCP, the role is Client, and the name is Std, the user only needs to fill in Std, and the system will splice the complete protocol name "Modbus TCP-Client-Std". The developer names the protocol according to the naming convention.
- Jar package (required): A jar package of the protocol program running on the platform, that is, the jar package running on EnOS™ Edge. The developer can click "Upload" to upload the protocol jar package.
- Description (required): The developer may fill in the description dialog with a description of the applicable scenario, version,etc. of the protocol.
- Protocol document (optional): The developer can click the "Upload" link to upload one document related to the protocol, and only one document is allowed to upload. If there are more than one document, please package it before uploading.
