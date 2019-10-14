# Parsing Custom Format Data

When creating a product, if you select **Custom** for the **Data Type** field, the device can send data in any format (like binary data) to the EnOS Cloud. If this is the case, you will need to create data parsing script to encode and decode the upstream and downstream data. This topic describes how to edit and debug the script in the EnOS Cloud, and how to upload it to the EnOS runtime environment.

## Before You Start

To create and manage data parsing script, you must have write access to the **Connectivity Management** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.9/access_policy).

## About this task

Limited by configuration, resources, or network bandwidth, some devices has to pass data through to EnOS Cloud instead of in JSON. Data that is passed through to EnOS Cloud is transformed into the JSON format that is defined by EnOS by using a data parsing script. Data that goes from EnOS Cloud to devices is also converted from the EnOS-defined JSON to binary data that the devices can understand.

EnOS provides developers with the following functions:

- Online script editing in JavaScript that dynamically checks grammar;
- Saving, editing from, and deleting drafts;
- Testing a script;
- Submitting a script to EnOS runtime environment.


This topic instructs you to do the following tasks:

1. Editing a parsing script;

2. Testing the script;

3. Submitting the script to runtime environment.


## Before you start

Select **Device Management > Product > New Product** to create a product. Fill in the required segments with **Data Type** set to **Custom**. Click **OK**.

.. image:: ../../../media/product_management_new_product.png


## Step 1: Edit a parsing script

1. After you created the product, select **View**. Click **Data Parsing** in the Product Details page.

  .. image:: ../../../media/product_detail_data_parsing.png

2. Create a data parsing script in JavaScript in the editor.


You can call the data parsing script in two ways. For upstream data from devices, EnOS uses `rawDataToJsonStr` to decode data passed through. For downstream data from EnOS Cloud, EnOS uses `jsonStrToRawData` to encode data in JSON into binary format that the devices can understand.

.. warning:: Make sure the messages upstream and downstream are published to the pass-through TOPICs as follows:

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/model/up_raw`
- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/model/up_raw_reply`


Downstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/model/down_raw`
- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/model/down_raw_reply`


For more information, see [Report Device Events​ (Passthrough)](../../../reference/mqtt/upstream/device_else/report_event_pass.html) and [Invoke Device Services (Passthrough)](../../../reference/mqtt/downstream/invoke_services_pass.html).


Define the two methods in the parsing script as follows:


```js
/**
* Convert data in JSON into binary data that devices can understand. Called when EnOS Cloud sends data to devices
* Parameter：jsonStr json string Cannot be empty
* Return：rawData byte[] Array Cannot be empty
**/
function jsonStrToRawData(jsonStr) {
return rawData;
}
/**
* Converted device data in custom format into JSON. Called when devices report data to EnOS Cloud.
* Parameter：rawData byte[] Array Cannot be empty
* Return：jsonStr json String Cannot be empty
**/
function rawDataToJsonStr(rawData) {
return jsonStr;
}
```


Select **Save Draft** to save what you have written. If you exit and resume editing the next time, you can either resume from draft or delete the draft.

.. note: - You cannot recover deleted drafts.
  - Drafts won't be submitted to runtime environment. Saved draft have no impact on submitted scripts.
  - Latest draft overrides the previous draft.


## Step 2: Testing the script


1. In **Analog Input**, enter simulating data. For Simulation Type, select **send data** for upstream testing or **receive data** for downstream testing.


2. Select **Run** .


EnOS will use the input data to test the parsing script and display the result in the Parsing Result section. If any error exists, you will be prompted to revise the script and test again.


### Testing upstream data parsing


1. Select **send data** for Simulation Type.


2. Enter the binary data for pass-through and select **Run** .


EnOS will call `rawDataToJsonStr` to convert binary data into JSON. View the output in Parsing Results.


### Testing downstream data parsing


1. Select **receive data** for Simulation Type.


2. Enter the JSON data and select **Run** .
EnOS will call `JsonStrToRawData` to convert JSON into binary data. View the output in Parsing Results.


## Step 3: Submitting script to EnOS runtime environment


Submit the script to EnOS runtime environment only after it passes testing to avoid failure in transmitting data upstream and downstream. The script in runtime environment will be updated in about 5 minutes after submitting.


.. note:: Do not submit the script until it passes testing at least once.


## Sample script

### Reporting measure points


1. Define the device as the following model:

   .. csv-table::
      :widths: auto

      "Measure point identifier", "Data type", "Length (byte)"
      "mp_int", "Integer", "4"
      "mp_string", "String", "Variable length"
      "mp_double", "Double", "8"
      "mp_array", "Array", "Variable length"
      "mp_int_quality", "Integer with quality", "5"

2. Define the measure points with the following identifiers in the binary protocol:

   .. csv-table::
      :widths: auto

      "MBinary data", "Identifier"
      "0x01", "mp_int"
      "0x02", "mp_string"
      "0x03", "double"
      "0x04", "array"
      "0x05", "mp_int_quality"

   The device reports measure points in the following format:

   .. csv-table::
      :widths: auto

      "Description", "Byte"
      "Message type. 0x01 indicates upstream message.", "1"
      "Request ID", "4"
      "No. of measure point 1 identifier", "1"
      "Length of measure point 1", "2"
      "Value of measure point 1", "Byte definition per measure point type"
      "ID of measure point 2 identifier", "1"
      "Length of measure point 2", "2"
      "Value of measure point 2", "Byte definition per measure point type"

   The list can run on with more measure points.

3. Fill in stimulating data:

   ```
   0x0100000014010004000025f502000a6162636465666768696a0300083ff23d70a3d70a3d0400140000000100000003000000050000000700000009050005000001a703
   ```

   The output of the sample binary script in JSON:


   ```json
   {
   "id": "20",
   "method": "thing.measurepoint.post",
   "version": "1.0",
   "params": {
   "measurepoints": {
   "mp_int": 9717,
   "mp_string": "abcdefghij",
   "mp_double": 1.14,
   "mp_array": [ 1, 3, 5, 7, 9 ],
   "mp_int_quality": {
   "value": 423,
   "quality": 3
   }
     },
   "time": <System timestamp when EnOS receives the message>
   }
   }
   ```


### Receiving data from EnOS Cloud

1. Define measure points

   Define the measure points in binary format as follows:

   .. csv-table::
      :widths: auto

      "Description", "Length (byte)"
      "Message type. 0x02 indicates measure point setting.", "1"
      "Request ID", "4"
      "ID of measure point 1 identifier", "1"
      "Length of measure point 1", "2"
      "Value of measure point 1", "Byte definition per measure point type"
      "ID of measure point 2 identifier", "1"
      "Length of measure point 2", "2"
      "Value of measure point 2", "Byte definition per measure point type"

   The list can run on with more measure points.

2. Define the input in JSON as follows:

   ```json
   {
   "id": "20",
   "method": "thing.service.measurepoint.set",
   "version": "1.0",
   "params": {
   "mp_int": 9717,
   "mp_string": "abcdefghij",
   "mp_double": 1.14,
   "mp_array": [1, 3, 5, 7, 9],
   "mp_int_quality": {
   "value": 423,
   "quality": 3
       }
     }
   }
   ```

   The binary output of this JSON sample is as follows:

   ```
   0x020000001401000025f502000a6162636465666768696a033ff23d70a3d70a3d040014000000010000000300000005000000070000000905000001a703
   ```

### Calling device services

1. Define service model as follows:

   .. csv-table::
      :widths: auto

      "Service identifier", "Input parameter", "Model of input", "Output paramater", "Output model"
      "service1", "input_int", "int", "output_int", "int"

2. Define the message that calls the serices in the following format:

   .. csv-table::
      :widths: auto

      "Description", "Byte"
      "Service to be called. 0x11 indicates calling the service1", "1"
      "Request ID", "4"
      "Value of input parameter input_int", "4"

3. Define the service request in JSON as follows:

   ```json
   {
   "method":"thing.service.service1",
   "id":1234,
   "version":"1.0",
   "params": {
   "input_int":12
   }
   }
   ```

   The output in binary of parsing the JSON input is as follows:

   ```
   0x11000004d2c80000000e
   ```

### Sample parsing script

```js
/**
* Definition of constants
*/
// Method Code for reporting measure points
var CODE_MEASUREPOINT_POST = 0x01;
//Method in standard JSON. Device reporting measure points to EnOS Cloud.
var METHOD_MEASURE_POINT_POST = 'thing.measurepoint.post';
//Method in standard JSON. EnOS Cloud sending measure point commands to devices.
var CODE_MEASUREPOINT_SET = 0x02;
var METHOD_MEASUREPOINT_SET = 'thing.service.measurepoint.set';
var CODE_MEASUREPOINT_SET_REPLY = CODE_MEASUREPOINT_SET;
// Method in standard JSON. EnOS Cloud sending service calls to devices. service1 is the service identifier.
var CODE_SERVICE_1 = 0x11; // service1 identifier
var METHOD_SERVICE_1 = 'thing.service.service1';
var CODE_SERVICE_1_REPLY = CODE_SERVICE_1;
/**
* rawDataToJsonStr function definition
* This method is called when a device sends message to EnOS Cloud. Binary data is converted into JSON.
* Parameter：rawData byte[]string Cannot be empty
* 出参：jsonStr json string Cannot be empty
*/
function rawDataToJsonStr(bytes) {
var uint8Array = new Uint8Array(bytes.length);
for (var i = 0; i < bytes.length; i++) {
uint8Array[i] = bytes[i] & 0xff;
}
var dataView = new DataView(uint8Array.buffer, 0);
var method_code = uint8Array[0]; // method code
var jsonMap = {}; // The return in JSON
if (method_code === CODE_MEASUREPOINT_POST) {
jsonMap = measurepoint_post(dataView);
} else if (method_code === CODE_MEASUREPOINT_SET_REPLY) {
jsonMap = measurepoint_set_reply(dataView);
} else if (method_code === CODE_SERVICE_1_REPLY) {
jsonMap = service_1_invoke_reply(dataView);
}
// return jsonStr;
return JSON.stringify(jsonMap);
}
// measurepoint ID
var codeMapMeasurepoint = {
0x01: "mp_int",
0x02: "mp_string",
0x03: "mp_double",
0x04: "mp_array",
0x05: "mp_int_quality"
};
/**
* Device sending measure points to EnOS Cloud.
* Input：
0x0100000014010004000025f502000a6162636465666768696a0300083ff23d70a3d70a3d0400140000000100000003000000050000000700000009050005000001a703
* Output：
{
"id": "20",
"method": "thing.measurepoint.post",
"version": "1.0",
"params": {
"measurepoints": {
"mp_int": 9717,
"mp_string": "abcdefghij",
"mp_double": 1.14,
"mp_array": [ 1, 3, 5, 7, 9 ],
"mp_int_quality": {
"value": 423,
"quality": 3
}
},
"time": 1550894467486
}
}
*/
function measurepoint_post(dataView) {
var jsonMap = {};
jsonMap['id'] = '' + dataView.getUint32(1); //in JSON - indicates the ID of this request
jsonMap['method'] = METHOD_MEASURE_POINT_POST; //in JSON - method for reporting measure points
jsonMap['version'] = '1.0'; //in JSON - Fixed field for protocol version
var params = {
'measurepoints': {},
'time': new Date().getTime()
};
var index = 5;
for (; index < dataView.byteLength;) {
var mp_code = dataView.getUint8(index); // Obtains measurepoint code, 1 byte
index += 1;
var length = dataView.getInt16(index); // Obtains value length, 2 byte
index += 2;
var value = null;
if (mp_code === 0x01 && length === 4) {
value = dataView.getInt32(index);
} else if (mp_code === 0x02) {
value = decodeString(dataView, index, length);
} else if (mp_code === 0x03 && length === 8) {
value = dataView.getFloat64(index);
} else if (mp_code === 0x04) {
value = decodeArray(dataView, index, length);
} else if (mp_code === 0x05 && length === 5) {
value = {};
value.value = dataView.getInt32(index);
value.quality = dataView.getInt8(index+4);
}
params.measurepoints[codeMapMeasurepoint[mp_code]] = value;
index += length;
}
jsonMap['params'] = params; // in JSON - standard field for params
return jsonMap;
}
/**
* Stimulated return from device
* Input：
0x020000001400c8
* Output：
{
"id": "20",
"code": "0",
"data": ""
}
*/
function measurepoint_set_reply(dataView) {
var jsonMap = {};
jsonMap['id'] = '' + dataView.getUint32(1); //in JSON. ID for this request
jsonMap['code'] = '' + dataView.getUint8(5);
jsonMap['data'] = '';
return jsonMap;
}
/**
* Stimulated return of service call
* Input：
0x11000004d2c80000000e
* Output：
{
"id": "1234",
"code": "200",
"data": {
"output_int": 14
},
"message": ""
}
*/
function service_1_invoke_reply(dataView) {
var jsonMap = {};
jsonMap['id'] = '' + dataView.getUint32(1);
jsonMap['code'] = '' + dataView.getUint8(5);
var data = {};
data['output_int'] = dataView.getInt32(6);
jsonMap['data'] = data;
var message = '';
if (dataView.byteLength > 10) {
message = decodeString(dataView, 10);
}
jsonMap['message'] = message;
return jsonMap;
}
function decodeString(dataView, index, length) {
var strArr = []; // new Uint8Array(length);
for (var i = 0; i < length; i++) {
strArr[i] = dataView.getInt8(i + index);
}
var str = String.fromCharCode.apply(String, strArr);
return str;
}
function decodeArray(dataView, index, length) {
var result = [];
for (var i = 0; i < length; i += 4) {
result = result.concat(dataView.getUint32(index + i));
}
return result;
}
/**
* jsonStrToRawData function definition
* This method is called when EnOS Cloud sends data to devices, converting data in JSON to binary.
* Parameter：jsonStr json string Cannot be empty
* Return：rawData byte[] array Cannot be empty
*/
function jsonStrToRawData(jsonStr) {
var json = JSON.parse(jsonStr); // jsonStr to jsonObject
var method = json['method']; // obtains method
var rawData = [];
if (isEmpty(method)) {
rawData = reply(json)
} else if (method === METHOD_MEASUREPOINT_SET) {
rawData = measurepoint_set(json);
} else if (method === METHOD_SERVICE_1) {
rawData = service_1_invoke(json);
}
return rawData;
}
/**
* Simulated return of reporting measure points
* Input：
{
"id":20,
"code":200,
"data":""
}
* Return：
0x0000001400c8
*/
function reply(json) {
var id = json['id']; // ['id'];
var code = json['code'];
var data = json['data'];
var rawData = [];
rawData = rawData.concat(buffer_int32(id));
rawData = rawData.concat(buffer_int16(code));
if (data !== '') {
rawData = rawData.concat(data);
}
return rawData;
}
// Measure point ID
var measurepointMapCode = {
"mp_int": 0x01,
"mp_string": 0x02,
"mp_double": 0x03,
"mp_array": 0x04,
'mp_int_quality': 0x05
};
/**
* Simulated measure point setting
* Input：
{
"id": "20",
"method": "thing.service.measurepoint.set",
"version": "1.0",
"params": {
"mp_int": 9717,
"mp_string": "abcdefghij",
"mp_double": 1.14,
"mp_array": [1, 3, 5, 7, 9],
"mp_int_quality": {
"value": 423,
"quality": 3
}
}
}
* Output：
0x020000001401000025f502000a6162636465666768696a033ff23d70a3d70a3d040014000000010000000300000005000000070000000905000001a703
*/
function measurepoint_set(json) {
var id = json['id'];
var version = json['version']; // field for version，included in rawData
var rawData = [];
rawData = rawData.concat(buffer_uint8(CODE_MEASUREPOINT_SET)); //field for command
rawData = rawData.concat(buffer_int32(parseInt(id))); // in JSON 'id'
//Concatenated per customized protocol format rawData
var params = json['params'];
for (var key in params) {
rawData = rawData.concat(measurepointMapCode[key])
if (key === 'mp_int') {
rawData = rawData.concat(buffer_int32(params[key]))
} else if (key === 'mp_string') {
rawData = rawData.concat(buffer_string(params[key]));
} else if (key === 'mp_double') {
rawData = rawData.concat(buffer_double64(params[key]))
} else if (key === 'mp_array') {
var arr_value = params[key];
rawData = rawData.concat(buffer_int16(arr_value.length * 4))
for (var i = 0; i < arr_value.length; i++) {
rawData = rawData.concat(buffer_int32(arr_value[i]))
}
} else if (key === 'mp_int_quality') {
var json_quality = params[key];
rawData = rawData.concat(buffer_int32(json_quality['value']));
rawData = rawData.concat(buffer_uint8(json_quality['quality']));
}
}
return rawData;
}
/**
* Simulated service calling
* Input：
{
"method":"thing.service.service1",
"id":1234,
"version":"1.0",
"params": {
"input_int":12
}
}
* Output：
0x11000004d20000000c
*/
function service_1_invoke(json) {
var id = json['id'];
var version = json['version'];
var payloadArray = [];
payloadArray = payloadArray.concat(buffer_uint8(CODE_SERVICE_1)); //field for command
payloadArray = payloadArray.concat(buffer_int32(parseInt(id))); //id in JSON
var params = json['params'];
var inparam_1 = params['input_int'];
payloadArray = payloadArray.concat(buffer_int32(parseInt(inparam_1)))
return payloadArray;
}
function isEmpty(obj) {
if (obj === undefined || obj === null || obj === "") {
return true;
} else {
return false;
}
}
//The following are auxiliary functions.
function buffer_uint8(value) {
var uint8Array = new Uint8Array(1);
var dv = new DataView(uint8Array.buffer, 0);
dv.setUint8(0, value);
return [].slice.call(uint8Array);
}
function buffer_int16(value) {
var uint8Array = new Uint8Array(2);
var dv = new DataView(uint8Array.buffer, 0);
dv.setInt16(0, value);
return [].slice.call(uint8Array);
}
function buffer_int32(value) {
var uint8Array = new Uint8Array(4);
var dv = new DataView(uint8Array.buffer, 0);
dv.setInt32(0, value);
return [].slice.call(uint8Array);
}
function buffer_double64(value) {
var uint8Array = new Uint8Array(8);
var dv = new DataView(uint8Array.buffer, 0);
dv.setFloat64(0, value);
return [].slice.call(uint8Array);
}
function buffer_string(value) {
var length = value.length;
var uint8Array = new Uint8Array(length + 2)
uint8Array[0] = Math.floor(length / 256);
uint8Array[1] = length % 256;
for (var i = 0; i < length; i++) {
uint8Array[i + 2] = value.charCodeAt(i);
}
return [].slice.call(uint8Array);
}
/**
* The following are methods for local testing.
*/
function TestJsonToRaw() {
var json = "{\n" +
" \"method\": \"thing.service.measurepoint.set\",\n" +
" \"id\": \"20\",\n" +
" \"params\": {\n" +
" \"mp_int\": 1163,\n" +
" \"mp_string\": \"abcdefg\"\n" +
" },\n" +
" \"version\": \"1.0\"\n" +
"}";
console.log(json);
var rawData = jsonStrToRawData(json);
console.log(rawData);
}
function TestRawToJson() {
// 0x0100000014010004000025f502000a6162636465666768696a0300083ff23d70a3d70a3d0400140000000100000003000000050000000700000009050005000001a701
var rawData = [
0x01, // method, thing.measurepoint.post
0x00, 0x00, 0x00, 0x14, // id
0x01, // mp_int code
0x00, 0x04, // length
0x00, 0x00, 0x25, 0xf5, // value
0x02, // mp_string code
0x00, 0x0a, // length
0x61, 0x62, 0x63, 0x64, 0x65, 0x66, 0x67, 0x68, 0x69, 0x6a, // value
0x03, // mp_double code
0x00, 0x08, // length
0x3f, 0xf2, 0x3d, 0x70, 0xa3, 0xd7, 0x0a, 0x3d, // value
0x04, // mp_array code
0x00, 0x14, // length
0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00, 0x05, 0x00, 0x00, 0x00, 0x07, 0x00, 0x00, 0x00, 0x09, // value
0x05, // mp_int_quality
0x00, 0x05, // length
0x00, 0x00, 0x01, 0xa7, // value
0x01 // quality
];
var jsonMap = rawDataToJsonStr(rawData);
console.log(jsonMap)
}
function TestService_1ToRawData() {
var json = "{\n" +
" \"method\": \"thing.service.service1\",\n" +
" \"id\": \"20\",\n" +
" \"params\": {\n" +
" \"input_int\": 1163\n" +
" },\n" +
" \"version\": \"1.0\"\n" +
"}";
console.log(json);
var rawData = jsonStrToRawData(json);
console.log(rawData);
}
// Methods to initiate local testing:
// TestRawToJson();
// TestJsonToRaw();
// TestService_1ToRawData();
```
