# Setting Measure Points and Invoking Services (Non-DTLS)

Applications can use EnOS open APIs to set measurepoints or invoke services for the devices connected via CoAP. The process is as follows:

.. image:: ../../../media/coap_downstream_flow_non_dtls.png

For more information about the data formats of the request and response, and parameter description, see [Sending Commands to Devices (Pass-through)](../../mqtt/downstream/invoke_services_pass) and [Invoke Device Services (Non-Passthrough)](../../mqtt/downstream/invoke_services_nopass).

When the application calls an API to set measurepoints or invoke services from a device, EnOS caches this request. When a device sends a request to EnOS, EnOS will include the following options in the response to the device's request.

- Option (2100): The request topic for issuing commands in non-passthrough mode.
- Option (2102): The payload of the command.

Format of the response sent to the device by EnOS is as follows:

```json
Code: CoAP return code
Payload: {RequestPayload}
Customized Option 2100:/topic/sys/${ProductKey}/${DeviceKey}/thing/model/down_raw
Customized Option 2102: {Down-Raw Payload}
``` 

In this response:

.. csv-table::
   :widths: auto

   "Parameter", "Description"
   "ProductKey", "Used for authentication."
   "DeviceKey",	"Used for authentication."
   "Payload", "The payload requested by the device."

After the device completes executing the command, it sends the execution result to EnOS based on the topic and parameter specifications defined by EnOS.

If more commands are to be issued, EnOS will continue to include the command payload in Option(2102).