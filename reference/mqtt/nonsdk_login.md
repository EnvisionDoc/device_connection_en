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
  mqttPassword: toUpperCase(sha256({content}{deviceSecret}/{productSecret}))
```
- For the **mqttClientId** segment:

  - `clientId`: Required. Identifier of the device. Can be either the MAC address or device serial number. It must contain no more than 64 characters. 

  - `securemode`: Required. Indicates the secure mode that has been used. 
    - For static authentication (`productKey`, `deviceKey`, `deviceSecret` is provided to statically activate the device), the value is `2`.
    - For dynamic authentication (`productKey`, `productSecret`, `deviceKey` is provided to dynamically activate the device) the value is `3`.
  - `signmethod`: Required. Indicates the signing method. Supports sha256, which means using SHA256 signature algorithm.
  - `timestamp`: Required. Indicates the UNIX timestamp of the current time in millisecond.

   For example, the values of parameters required for `mqttClientId` are as follows:
   - `clientId`=id123456
   - `securemode`=2 for static authentication
   - `sighmethod`=sha256
   - `timestamp`=1234567890 

   Then the `mqttCliendId` in this case would be `clientIdid123456|securemode=2,signmethod=sha256,timestamp=1234567890|`

- For the **mqttUsername** segment: Concatenated by `deviceKey`, "&", and `productKey`.

  - `deviceKey`: Device key of a product. Can be found on the EnOS Console after you register the device.
  - `productKey`: Product key of a product. Can be found on the EnOS Console after you register the device.

  For example, the `deviceKey` is `abcdefg` and `productKey` is `1234567`. Then `mqttUsername` in this case would be `abcdefg&1234567`.

- For the **mqttPassword** segment: For static authentication,  a string concatenated by `content` and `deviceSecret`. For dynamic authentication, a string concatenated by `content` and `productSecret`. Then use SHA256 algorithm to generate a new string from this string and turn the new string into upper case letters.

  <!-- - You can use the [Password Generation Tool](../../`static/nonsdk`enosmqttsign`index.html) to generate the password quickly.-->

   - `content`: concatenated by `clientId` and its value, `deviceKey` and its value, `productKey` and its value, `timestamp` and its value.

     For example, the values of parameters required for `content` are as follows:
     - `clientId` = id123456
     - `deviceKey` = dK987654
     - `productKey` = pK11111
     - `timestamp` = 1234567890

     Then `content` = `clientIdid123456deviceKeydK987654productKeypK11111timestamp1234567890`

   - `deviceSecret`: Device secret of a device. You can find it in EnOS console.
   - `productSecret`: Product secret of a device. You can find it in EnOS console.

   Either `deviceSecret` or `productSecret` should be appended to `content` without space.

### Static Authentication

In secret-per-device authentication, `productKey`, `deviceKey`, and `deviceSecret` are configured in the device before the device tries to get authenticated and log in to EnOS. You can obtain a device's `productKey`, `deviceKey`, and `deviceSecret` from EnOS console after you have created the device in **Device Management > Device**.

For secret-per-device authentication:
```java
mqttPassword: toUpperCase(sha256({content}{deviceSecret}))
```

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

### Dynamic Authentication

To enable secret-per-product authentication, you must first toggle **Enable Dynamic Activation** switch on in **Device Management > Product > Product Details**.

For secret-per-product authentication,:

```java
mqttPassword: toUpperCase(sha256({content}{productSecret}))
```

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
