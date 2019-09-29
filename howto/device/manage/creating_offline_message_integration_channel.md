# Integrating Device Data From Offline Channel

By creating an offline message integration channel, users can import the historical data from a 3rd-party system to EnOS for processing, storing and computing based on your policies.

## About This Task

To allow the data from a 3rd-party system to be integrated to EnOS, you must create an offline message integration channel for the 3rd-party system. After the historical data has been integrated into EnOS through the offline message integration channel, that data can be used by applications by calling EnOS APIs. Alternatively, the data can also be sent to Kafka topics for offline data based on pre-defined rules. EnOS data asset management functions can then use that data by subscribing to the topics.

## Before You Start

- The product that the device belongs to has been created. For information about how to create a product, see [Creating Products](creating_product)。
- You should have the permissions for device management; if not, contact your OU administrator to grant such permissions. See [Policies, Roles and Permissions](/docs/iam/en/latest/access_policy).

## Procedure

1. Select **Device Management > Message Integration**.

2. Select **New Channel**;

3. Enter the following information in the **New Message Channel** popups:

  .. csv-table::
     :widths: auto

     "Fields", "Descriptions"
     "Integration Mode", "Required. Refers to the type of data that you  want to integrate from a 3rd-party system."
     "Channel Name", "Required. Supports Chinese characters, upper and lower cases, numbers and underline (_). 4 to 32 characters. The channel name must be unique within this OU."
     "Protocol", "Required. Refers to the protocol used for 3rd-party offline message integration. Currently supports MQTT."
     "Target Product", "Required. Refers to the product which the integrated data belongs to. Every product only supports one offline message integration channel."
     "Data Format", "Required. Refers to the format of 3rd-party offline message. Supports JSON and custom format (pass-through). It is auto-filled when you have selected the Target Product."
     "Description", "Optional. Supports Chinese characters, upper cases and lower cases, numbers and underline (_). No more than 100 characters."

4. Select **Confirm** to complete the creation of message channel.

## Results

In **Device Management > Message Integration**, you may view the successfully created offline message channel.




