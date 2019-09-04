# CoAP-based Connection and Communication (Non-DTLS)

EnOS supports CoAP-based communication. The CoAP protocol is suitable for resource-limited and low-power devices, especially for NB-IoT devices. This topic describes the CoAP-based connection process without DTLS.

The CoAP protocol specifications and limits used by CoAP connection without DTLS are the same as those with DTLS. See [CoAP-Based Connection](../../../learn/enos_coap).

## Data Security

Device need to have SHA-256 and AES-128 encryption capability to generate digital signature for authentication.

## Connection Process

The process for connecting NB-IoT devices to EnOS via CoAP is shown as follows:

.. image:: ../../../media/coap_connection_process_non_dtls.png

1. Obtain product key and product secret by creating a model and then a related product in EnOS.

2. Create a device in EnOS. Obtain the device secret.
   
3. Configure the authentication keys, that is, productKey, deviceKey, and deviceSecret on the device.
    
4. Power the device up and get the device online.

5. The device initiates an authentication request with productKey, deviceKey, deviceSecret, and digital signature.

6. EnOS authenticates the device. The device gets activated.

7. EnOS sends an AKC message to the device to inform that the device has been authenticated and is online.

## Before You Start

- Register devices one by one and obtain their `productKey`, `deviceKey` and `deviceSecret`. For information about how to register a device, see [Registering Devices](../../../howto/device/manage/creating_device).
- Ensure that the device has SHA-256 encryption capabilities.

## Connecting to the CoAP Server

The IP address of the CoAP server is `coap-<hostname>`, where `hostname` is where EnOS is located. For example, if EnOS is located on *abc.def.com*, then the CoAP server address would be *coap-abc.def.com*.

## Authentication and Connection

1. The device sends an authentication request in the following format:

   ```json
    POST /auth/${ProductKey}/${DeviceKey}
    Payload: { "secureMode": ${SecureMode}, "lifetime": ${Lifetime}, "sign": ${sign} }
   ```
   In this request:

   .. csv-table::
      :widths: auto

      "Parameter", "Description"
      "ProductKey", "Used for authentication."
      "DeviceKey",	"Used for authentication. It should be unique within an OU. You may use the IMEI of an NB-IoT device as its device key."
      "SecureMode", "SecureMode is set as 2 CoAP connection without DTLS."
      "Lifetime", "Used to determine the online status of device. A device would be determined offline if it doesn't not exchange any message with EnOS within Lifetime. Lifetime is counted in seconds and the value range is 30 to 86400."
      "Sign",	"Digital signature used for authentication."

    A digital signature is generated in the following procedure:

    1. Concatenate the following fields in the following order:`deviceKey${DeviceKey}lifetime${Lifetime}productKey${ProductKey}secureMode${secureMode}`
   
    2. Attach `${DeviceSecret}` to the end of the concatenated string:`deviceKey${DeviceKey}lifetime${Lifetime}productKey${ProductKey}secureMode${secureMode}${DeviceSecret}`
   
    3. Calculate the SHA-256 hash of the string generated in the previous step and capitalize all letters.

2. The device is authenticated. EnOS includes a CoAP return code in the response. The format of the response is as follows:
 
   ```json
      Code: CoAP return code.
   ```







 

 

