# Securing Connection Through Certificate-Based Authentication (Java)

This article helps you quickly get started with using certificate-based authentication to secure connection between the device and the cloud.

## Before You Start

The preparation work is as shown in the following diagram.

.. image:: ../media/certificate_preparation.png

### Step 0: Create Model, Product, and Device

This step supposes assumes that you have complete the following tutorials:

- [Connecting a Smart Device into EnOS Cloud](gettingstarted_device_connection)
- [Connecting a Non-smart Device to EnOS Cloud via Edge](gettingstarted_edge_connection).

**Creating an edge gateway product**

This step is almost the same as what you've done in the aforementioned tutorials with the only difference that you need to enable **Certificate Authentication** when you create the edge gateway product as shown in the following screenshot:

.. image:: ../media/edge_ssl.png

The inverter product does not need to have **Certificate Authentication** enabled because the inverter connects to the EnOS Cloud through the edge gateway. You only need to enable the authentication for the connection between the edge and the cloud.

**Creating edge device**

Create an edge device instance named **Edge01_Certificate** based on the product that you just created.

.. image:: ../media/edge01_certificate.png

Take note of the device triple of the **Edge01_Certificate** device, which will be used for creating the CSR. The following device triple is an example for your reference, you'll need to use your own.

- Product Key: `Et***YP6`
- Device Key: `UB***rOhJD`
- Device Secret: `jgWGPE***B7bShf2P5cz`

**Creating sub-device**

Follow [Connecting a Smart Device into EnOS Cloud](gettingstarted_device_connection) to create an inverter device as shown in the following figure:

.. image:: ../media/INV002.png


### Step 1: Create CSR File and Private Key

Use openssl to create an CSR file named `edge.csr` and a private key named `edge.key` with the following command:

```shell
openssl req -new -newkey rsa:2048 -out edge.csr -keyout edge.key -subj /C=CN/ST=Shanghai/L=Shanghai/O=EnOS/OU="Edge Service"/CN="UB***rOhJD" -passout pass:123456  -sha256 -batch
```

- The CSR file is used for requesting certificate from EnOS Cloud
- The private key is used for decrypting the data that is encrypted by the certificate.

For the guidelines of creating CSR file, see [Creating your Certificate Signing Request (CSR) file](https://www.envisioniot.com/docs/enos/en/latest/security/x509_ca/creating_csr.html)。

### Step 2: Invoke REST API to Request for Certificate

After the CSR file `edge.csr` is created, invoke the relevant EnOS API to request for certificate. You have obtained the device triple when you create the **Edge01_Certificate** device, you can now call the `applyCertificateByDeviceKey` API to obtain your certificate.

.. image:: ../media/postman_getcertificate.png

After you obtain the certificate, save it as `edge.pem`. It takes about 30 seconds for the certificate to take effect.

### Step 3: Obtain Root Certificate

Download the CA root certificate and save it as `cacert.pem` from the address given as value to `certChainURL` in Step 2.

### Step 4: Use Keytool to Generate JKS File

Run the following commands to generate the `edge.jks` file.

#### a.Check file

```shell
[root@DemoMachine cert]# ll
total 12
-rw-r--r-- 1 root root 1395 Nov 28 19:51 cacert.pem
-rw-r--r-- 1 root root 1858 Nov 28 19:51 edge.key
-rw-r--r-- 1 root root 1416 Nov 28 20:08 edge.pem
```

#### b.Export certificate and private key as .p12 file

```shell
[root@DemoMachine cert]# openssl pkcs12 -export -in edge.pem -inkey edge.key -out edge.p12 -name edge -CAfile cacert.pem -caname cacert
Enter pass phrase for edge.key:
Enter Export Password:
Verifying - Enter Export Password:
```

#### c.Check the generated .p12 file

```shell
[root@DemoMachine cert]# ll
total 16
-rw-r--r-- 1 root root 1395 Nov 28 19:51 cacert.pem
-rw-r--r-- 1 root root 1858 Nov 28 19:51 edge.key
-rw-r--r-- 1 root root 2654 Nov 28 20:19 edge.p12
-rw-r--r-- 1 root root 1416 Nov 28 20:08 edge.pem
```

#### d.Import the .p12 file into the keystore

```shell
[root@DemoMachine cert]# keytool -importkeystore -deststorepass 123456 -destkeypass 123456 -destkeystore edge.jks -srckeystore edge.p12 -srcstoretype PKCS12 -srcstorepass 123456 -alias edge
Importing keystore edge.p12 to edge.jks...

Warning:
The JKS keystore uses a proprietary format. You are recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore edge.jks -destkeystore edge.jks -deststoretype pkcs12".
```

#### e.Check the JKS file

```shell
[root@DemoMachine cert]# ll
total 20
-rw-r--r-- 1 root root 1395 Nov 28 19:51 cacert.pem
-rw-r--r-- 1 root root 2356 Nov 28 20:20 edge.jks
-rw-r--r-- 1 root root 1858 Nov 28 19:51 edge.key
-rw-r--r-- 1 root root 2654 Nov 28 20:19 edge.p12
-rw-r--r-- 1 root root 1416 Nov 28 20:08 edge.pem
```

#### f.Verify that the JSK has one trusted certificate entry

```shell
[root@DemoMachine cert]# keytool -list --keystore edge.jks
Enter keystore password:  
Keystore type: jks
Keystore provider: SUN

Your keystore contains 1 entry

edge, Nov 28, 2018, PrivateKeyEntry,
Certificate fingerprint (SHA1): 38:16:5A:1F:1D:68:44:44:FE:56:1A:84:36:31:85:CB:14:5B:9C:5E

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore edge.jks -destkeystore edge.jks -deststoretype pkcs12".
```

#### g.Import cacert root certificate into the keystore

```shell
[root@DemoMachine cert]# keytool -import -trustcacerts -alias cacert -file cacert.pem -keystore edge.jks -storepass 123456
Owner: EMAILADDRESS=ca@eniot.io, CN=EnOS CA, OU=EnOS CA, O=EnOS, L=Shanghai, ST=Shanghai, C=CN
Issuer: EMAILADDRESS=ca@eniot.io, CN=EnOS CA, OU=EnOS CA, O=EnOS, L=Shanghai, ST=Shanghai, C=CN
Serial number: 8c54a99157c8ef28
Valid from: Mon Nov 19 18:20:27 CST 2018 until: Thu Nov 16 18:20:27 CST 2028
Certificate fingerprints:
	 MD5:  4E:BF:2A:53:85:1E:21:97:70:72:AD:DF:A5:79:51:3F
	 SHA1: 96:BC:6B:F0:15:CD:BB:03:52:12:A2:C6:C4:BD:20:69:71:4A:75:C2
	 SHA256: 81:B0:E3:01:D3:2B:48:E7:CF:CC:BC:07:9A:AD:49:74:EF:92:97:A1:D4:46:E2:4E:56:94:14:32:A7:09:FA:9F
Signature algorithm name: SHA256withRSA
Subject Public Key Algorithm: 2048-bit RSA key
Version: 3

Extensions:

#1: ObjectId: 2.5.29.35 Criticality=false
AuthorityKeyIdentifier [
KeyIdentifier [
0000: AE 4F F7 AF A7 19 7B 0B   AE 2E 79 0F B4 7B E5 AE  .O........y.....
0010: 8C F4 54 0D                                        ..T.
]
]

#2: ObjectId: 2.5.29.19 Criticality=false
BasicConstraints:[
  CA:true
  PathLen:2147483647
]

#3: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: AE 4F F7 AF A7 19 7B 0B   AE 2E 79 0F B4 7B E5 AE  .O........y.....
0010: 8C F4 54 0D                                        ..T.
]
]

Trust this certificate? [no]:  yes
Certificate was added to keystore

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore edge.jks -destkeystore edge.jks -deststoretype pkcs12".
```

#### h.Verify that the JKS has two trusted certificate entries

```shell
[root@DemoMachine cert]# keytool -list --keystore edge.jks
Enter keystore password:  
Keystore type: jks
Keystore provider: SUN

Your keystore contains 2 entries

cacert, Nov 28, 2018, trustedCertEntry,
Certificate fingerprint (SHA1): 96:BC:6B:F0:15:CD:BB:03:52:12:A2:C6:C4:BD:20:69:71:4A:75:C2
edge, Nov 28, 2018, PrivateKeyEntry,
Certificate fingerprint (SHA1): 38:16:5A:1F:1D:68:44:44:FE:56:1A:84:36:31:85:CB:14:5B:9C:5E

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore edge.jks -destkeystore edge.jks -deststoretype pkcs12".
[root@DemoMachine cert]#
```

## Configuring Certificate for the Use of Java SDK

### Step 5: Configure the JKS File in Java sample program

In the sample program, enter the URL to the JKS file and the password to the file as shown in the following code snippet:

```Java
public static void initSSLConnection() {
    System.out.println("start connect with callback ... ");
    try {
        client = new MqttClient(betaSSL, productKey, deviceKey, deviceSecret);
        client.getProfile().setConnectionTimeout(60).setSSLSecured(true)
        .setSSLJksPath("edge.jks" , "123456");
        client.connect(new IConnectCallback() {
            @Override
            public void onConnectSuccess() {
                System.out.println("connect success");
            }

            @Override
            public void onConnectLost() {
                System.out.println("onConnectLost");
            }

            @Override
            public void onConnectFailed(int reasonCode) {
                System.out.println("onConnectFailed : " + reasonCode);
            }

        });
    } catch (EnvisionException e) {
        //e.printStackTrace();
    }
    System.out.println("connect result :" + client.isConnected());
}

```

### Step 6: Start Sample Program

Start the sample program and view the logs.

## Verify Connection

### Step 7: Check Device Connection Status

After you run the sample program, the edge device logs in and adds sub-devices into its topology, proxies the sub-devices to connect to the cloud. The device connection status is as shown in the following figure:

.. image:: ../media/device_list.png

### Step 8: Check Device Data

Go to the console, select **Connection Management > Device Management**, then go to **Device Details**, open the **Measure Point** tab, and select a measure point, click **View Data** to check the historical data records.
