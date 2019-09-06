# Common Parameter Description

## Common Parameters in Request and Response

The following table describes the common parameters used in the request and response messages.

.. list-table::
   :widths: auto

  * - Parameter
    - Type
    - Occurrence
    - Description
  * - id
    - String
    - Optional
    - Message ID. Reserved parameter for future use.
  * - version
    - String
    - Mandatory
    - Version of the protocol.
  * - params
    - JSON
    - Mandatory in request
    - Request parameters, in JSON format, Can either be array or dict.
  * - method
    - String
    - Mandatory in request
    - Name of the method.
  * - code
    - Integer
    - Mandatory in response
    - Response code. For a list of common return codes, see `Common Return Code`_
  * - data
    - JSON
    - Optional
    - Detailed infomaton, in JSON format. Can be either int or dict regarded to the returned message.  

## Common Return Code

The following list contains common return codes that apply to all MQTT topics of EnOS. For return codes that are unique to an MQTT topic, go to the specific topic explanation articles in this "Device Protocol for MQTT" section.

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1220 | Payload format error | Payload contains invalid JSON format |
| 1251 | Payload is empty | Payload does not contain any content |
| 1260 | Methods not consistent | The method cannot be used for the topic |
| 1008 | Msg size is too large, discard the message | The MQTT message size is too large |
| 1009 | Publish to topic with no write permission | The MQTT topic is not supported. Or the device that tried to publish data to the MQTT topic hasn't logged in to EnOS |






