# Common Parameter Description

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
    - Response code.
  * - data
    - JSON
    - Optional
    - Detailed infomaton, in JSON format. Can be either int or dict regarded to the returned message.  

<!--This is the End-->
