# Cloud to Cloud Real-time Data Forwarding

## Scenario

In this scenario, the devices are connected to a 3-rd party cloud. To use the services or applications of EnOS, a solution needs to forward the data ingested from the 3-rd party cloud to the EnOS Cloud.

## Solution

The 3-rd party cloud can be seen as an application on EnOS, let’s suppose the application name is Data Forwarding App. This application will abstract the data from the 3rd-party cloud and call the EnOS REST API to report the device telemetry.

.. image:: ../../media/cloud2cloud.png

### Part I. EnOS Cloud Setup

1. Model and register the devices on EnOS Cloud

2. Register the Data Forwarding App to obtain the service account.

### Part II. Data Forwarding App abstracts, transforms, and forwards data

1. Configure the Data Forwarding App with the service account that will be used for EnOS to authenticate the application.

2. The 3rd-party cloud acts as the data source, the Data Forwarding App reads the master data of devices from the 3rd-party cloud, to obtain the device data forwarding list (list of measure points?).


3. The Data Forwarding App abstracts device data, transforms the data into the format that is compliant with EnOS device data standard.

4. The Data Forwarding App calls EnOS API to report data to EnOS cloud.

### Part III. New device onboarding

1. The Data Forwarding App listens on the master data update, when a new device is detected:

2. The Data Forwarding App calls REST APIs to register a device on EnOS Cloud.

3. The Data Forwarding App adds the device to the data forwarding list.

## Implementation

### Part I. EnOS Cloud Setup

1. [Create the device model](../../howto/model/creating_model), note the identifier of the model points. You'll later report measure point data to the model points.

2. [Create the product](../../howto/device/manage/creating_product). The product configuration defines the how a collection of devices send data to the EnOS Cloud and how to secure the connection between devices and cloud.

3. [Register the device](../../howto/device/manage/creating_device). You'll get the device identity
`deviceKey` that you'll use when mapping the device between 3rd-party cloud and EnOS cloud.

4. [Register the application](/docs/app-development/en/2.0.8/managing_apps.html#registering-an-application) to obtain the service account: `accessKey` and `secretKey`, which the application will need to call EnOS APIs.

### Part II. Develop the Data Forwarding App

1. Configure the service account into the application.

2. Map the device master data between the 3rd-party cloud and the EnOS Cloud.

   .. note:: Use the deviceID in the 3rd-party as the deviceKey in EnOS (to uniquely identify the device). Every device might be associated to a product in the EnOS Cloud.

3. The Data Forwarding App uses the `getDeviceByDeviceKey` API to obtain the device `assetId` based on `deviceKey`. You will need `assetId` in the next step because measure point data reporting is against `assetId`.

4. The Data Forwarding App uses the `uploadDeviceMeasurepoints` API to report measure point data to EnOS Cloud.

5. The Data Forwarding App uses the `registerDevices API` to register the new device on EnOS.

For how to use EnOS APIs, see [Getting Started with EnOS REST APIs](/docs/app-development/en/2.0.8/gettingstarted_api.html).

.. list-table::
   :widths: auto

   * - 3rd party Cloud DeviceID
     - EnOS ProductKey
     - EnOS AssetId
   * - SN000001
     - XmrETyvF
     - Ji7bipvy
   * - SN000002
     - EtHYdYP6
     - 3kxJLXgg

<!--end-->
