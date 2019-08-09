# Establishing Connection with EnOS Cloud using the MQTT Protocol

This article instructs how to establish connection from devices to the EnOS Cloud through the MQTT protocol.

The supported MQTT version:

- MQTT v3.1.1 on port 11883 if you use the secret-based one-way authentication.
- MQTT v3.1.1 over SSL/TLS on port 18883 if you use the certificate-based two-way authentication.


## Using the MQTT Protocol Directly

You can connect devices to the EnOS Cloud using the MQTT protocol directly. Include the following values in the CONNECT packet of the device:

```java
  mqttClientId: {clientId}|securemode={secureMode}, signmethod=sha256,timestamp={timeStamp}|
  mqttUsername: {deviceKey}&{productKey}
  mqttPassword: toUpperCase(sha256(content{deviceSecret}/{productSecret}))
```
- For the **mqttClientId** segment:

  - `clientId`: Required. Identifier of the device. Can be specified using either the MAC address or device serial number. It must contain no more than 64 characters. 

  The parameters within ``||`` are the optional parameters. 

  - `securemode`: Required. Indicates the secure mode that has been used. 
    - For secret-per-device authentication (`productKey`, `deviceKey`, `deviceSecret` is provided to statically activate the device), the value is `2`.
    - For secret-per-product authentication (`productKey`, `productSecret`, `deviceKey` is provided to dynamically activate the device) the value is `3`.
  - `signmethod`: Required. Indicates the signing method. Supports sha256, which means using SHA256 signature algorithm.
  - `timestamp`: Required. Indicates the UNIX timestamp of the current time in milliseconds.

- For the **mqttUsername** segment:

  - The value of the `deviceKey` and `productKey` of a device that can be found on the EnOS Console after you complete creating the device.

- For the **mqttPassword** segment:

  <!-- - You can use the [Password Generation Tool](../../`static/nonsdk`enosmqttsign`index.html) to generate the password quickly.-->

 Different methods are used to generate `mqttPassword` for devices that use secret-per-device and secret-per-product authentication.

### Secret-per-device Authentication

In secret-per-device authentication, `productKey`, `deviceKey`, and `deviceSecret` are configured in the device before the device tries to get authenticated and log in to EnOS. You can obtain a device's `productKey`, `deviceKey`, and `deviceSecret` from EnOS console after you have created the device in **Device Management > Device**.

For secret-per-device authentication:
```java
mqttPassword: toUpperCase(sha256({content}{deviceSecret}))
```

Generate an MQTT password by combining `content` and `deviceSecret`, whose definitions are explained as follows:

  - `content`: The concatenation of `clientID`, `deviceKey`, `productKey`, `timestamp`, and their values. The parameter names must be sorted in alphabetical order and concatenated without any space or character.
  - `deviceSecret`: Append the value of `deviceSecret` after `content` without any space or character. 

.. note:: The value of `timestamp` must be same as the `timestamp` in the **mqttClientId** segment.

Below is an example of **mqttPassword**, given:
- `clientId`=`123456`, 
- `deviceKey`=`test`, 
- `productKey`=`654321`, 
- `timestamp`=`1548753362502`, 
- `deviceSecret`=`abcdefg`.

The `clientId` in this case should be:

```java
123456|securemode=2,signmethod=sha256,timestamp=1548753362502|
```

The `mqttUsername` in this case should be:

```java
test&654321
```

The `mqttPassword` in this case should be:

```java
mqttPassword = toUpperCase(sha256(clientId123456deviceKeytestproductKey654321timestamp1548753362502abcdefg))
```

### Secret-per-product Authentication

To enable secret-per-product authentication, you must first toggle **Enable Dynamic Activation** switch on in **Device Management > Product > Product Details**.

For secret-per-product authentication,:

```java
mqttPassword: toUpperCase(sha256({content}{productSecret}))
```
Generate an MQTT password by combining `content` and `productSecret`, whose definitions are explained as follows:

  - `content`: The concatenation of `clientID`, `deviceKey`, `productKey`, `timestamp`, and their values. The parameter names must be sorted in alphabetical order and concatenated without any space or character.

  - `productSecret`: Append the value of `productSecret` after `content` without any space or character. 

.. note:: The value of `timestamp` must be same as the `timestamp` in the **mqttClientId** segment.

Below is an example of `mqttPassword`, given:
- `clientId`=`123`, 
- `deviceKey`=`test`, 
- `productKey`=`123`, 
- `timestamp`=`1524448722000`, 
- `productSecret`=`abcdefg`.

The `mqttPassword` in this case should be:
```java
mqttPassword = toUpperCase(sha256(clientId123deviceKeytestproductKey123timestamp1524448722000abcdefg))
```

In secret-per-product authentication, `productKey`, `productSecret`, and `deviceKey` are configured in the device in advance. When the device tries to get authenticated and log in to EnOS, it first sends a request containing `productKey`, `productSecret`, and `deviceKey`in exchange for `deviceSecret`. If the device passes authentication, it then subscribes to the following topic to obtain the `deviceSecret`:

```
/ext/session/{productKey}/{deviceKey}/thing/activate/info
```

The `deviceSecret` is sent back as JSON file in the following format:

```json
{
    "id": "1",
    "version": "1.0",
    "method": "thing.activate.info",
    "params":{
        "assetId": "12344",
        "productKey": "1234556554",
        "deviceKey": "deviceKey1234",
        "deviceSecret": "xxxxxx"
    }
}
```

The device can then use the `deviceSecret` together with `productKey` and `deviceKey` for later authentication and logins.

<!--end-->
