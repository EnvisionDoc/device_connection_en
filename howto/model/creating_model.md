# Creating a Model

A model is an abstraction of the product's features, and defines what the product is, what it can do, and what services it can provide. This topic describes how to create a model.

## Before You Start
The features that are required for the model have been designed. For information about how to design features for a model, see [Thing Model Overview](model_overview).

## About This Task

Configure all the features in EnOS according to the defined thing model.

- Attributes
- Measuring points
- Service
- Event

## Step 1: Creating a Model

1. In the EnOS Console, select **Model** to open the Models page.

2. Click the **New Model** button and complete the following settings on the **New Model** pop-up window:

   - **Identifier**: A unique identifier of the model.
   - **Model Name**: The name of the model; duplicate name is not allowed in the same organization.
   - **Category**: Static extension info of the product, such as manufacturer and device model.
   - **Create From**:

     - No: Do not configure specific attributes for now. Manually define the features after the template is created.
     - Clone: Copy all the information of the source model. There is no association between the source model and the model created by cloning it.
     - Inherit: Copy all the information of the source model. The created model is associated with the source model.    
       - The child model will inherit all the features of its parent model, and the child template cannot modify the features in the parent template.
       - The child model can add new features based on the parent model, but it needs to use a different name from the parent model.
       - The child model can be further inherited to support multi-level inheritance.
       - Changes to the features in the parent model will affect the child template.

   - **Support Passthrough**: indicates whether to support reporting measuring point data that is not defined in the model.
   - **Source Model**: The model selected for copying or the inherited parent model.
   - **Description**: Detailed description of the model.

3. Click **OK**.

## Step 2: Defining features for the Model

1. From the list of created models, find the model that needs feature definition and click **Edit**.

2. Click the **Feature Definition** tab and click **Add** to set one or more features according to the product attributes.

### Adding Attributes

1. On the **Add Feature** page, select **Attributes** from the **Feature Type** dropdown list and complete the following settings:

   - **Name**: Required. The name of the attribute; duplicate name is not allowed under the same module.
   - **Identifier**: Required. A unique identification code for the attribute; duplicate identifier is not allowed under the same organization. This identifier value in JSON format is used as a key by the device to send attribute data. The cloud will verify the identifier before deciding whether to receive the data.
   - **Data Type**: Required. The data type of this attribute.

     + int, float, double: Information such as the default value and the data unit shall be defined.
     + enum: The parameter value of enum items and parameter description shall be defined.

       .. csv-table::
          :widths: auto

          "Field", "Description"
          "Parameter Value", "Supports int and string. No longer than 32 characters. Only if all values are int, will all the parameters be saved as integers.  If one or multiple values are string, all the values are saved as string."
          "Paramater Description", "Supports Chinese, upper and lower case letters, numbers and special characters that include “-”, “_”, “@”, “.”, and “:”. No more than 128 characters."  
   
       .. note:: Whether parameter values are saved as int or string depends on the definition you made when creating those values. 
           - If all values are saved as int when created, subsequent new or changed values will also be saved as int. A string value would cause an error message "invalid parameter type".
           - If all values are saved as string when created, subsequent new or changed values will also be saved as string. Even if you change the values later so that all of them are integers, they are still saved as string instead of int. No error message will be displayed in this scenarion.

     + bool: Boolean value shall be defined as 0 or 1.
     + string: The length is required.
     + timestamp: The timestamp is in the form of a UTC timestamp string (in milliseconds).
     + date: The format of date is yyyy-MM-dd HH:mm:ss.
     + struct: A JSON structure defined by **Parameter Name**, **Identifier**, **Data Type** and **Unit**.
     + array: **Type** shall be defined. Make sure that the same type is used in the same array.
     <!--+ map: it is used to indicate that, whether this model point can be mapped or not.-->
     + file: File type parameter, value is the file path in URI format.

   - **Required**: If set to "Yes", the parameter must be provided when creating a new device under this product.
   - **Description**: A description of the attribute.

2. Click **OK** to finish creating the attribute.

3. To create more attributes, repeat the above steps.

### Adding Measuring Points

1. On the **Add Feature** pop-up window, select **Measuring Points** from the **Feature Type** drop-down list and complete the following settings:

   - **Name**: The name of the measuring point; duplicate name is not allowed under the same module.

   - **Identifier**: A unique identification code for the measuring point; duplicate identifier is not allowed under the same organization. This identifier value in JSON format is used as a key by the device to send attribute data. The cloud will verify the identifier before deciding whether to receive the data.

   - **Point Type**: Select the type of the measuring point, which usually distinguishes the features of data ingested by measuring points. When storing data, different types of measurement data can be stored separately. When reading data, different types of measurement data can also be processed differently. Description of each point type is as follows:

     - AI: AI type measuring point usually ingests time series data of numerical type. When reading data, you can choose to aggregate data with specific algorithms.
     - DI: DI type measuring point usually ingests device status data. When reading data, you can choose to query the device status change data only.
     - PI: PI type measuring point is usually used for calculating electric power data. When reading data, you can choose to aggregate data with specific algorithms.
     - Generic: Measuring point for all other types of data, especially data of non-numerical type.

     .. note:: If measuring point data has been stored in TSDB, the point type cannot be modified. Change of the point type may result in failure of querying stored historical data. For more information, see [Configuring TSDB Storage](/docs/data-asset/en/latest/configuring_tsdb_storage.html). 

   - **Quality Indicator**: Whether the measuring point is an indicator of the data quality. If set to **Yes**, the configurations are as follows:

     + Value: Sets the data type of the measuring point. Only three types are supported: int, float, and double.
     + Quality Indicator: Data quality indicator in the form of array that is predefined by the system; cannot be changed. You can click **View** to see the predefined quality indicator parameters as well as their descriptions.

   - **Data Type**: If the test is not about quality indicator, the data type of the measuring point shall be configured.
     + int, float, double: Information such as the default value and the data unit shall be defined.
     + enum: The parameter value of enum items and parameter description shall be defined.
     + bool: Boolean value shall be defined as 0 or 1.
     + string: A default value, the string type and string length shall be defined.
     + timestamp: The timestamp is in the form of a UTC timestamp string (in milliseconds).
     + date: The format of date is yyyy-MM-dd HH:mm:ss.
     + struct: A JSON structure defined by **Parameter Name**, **Identifier**, **Data Type** and **Unit**.
     + array: describe whether this model point is the array type or not, **Type** shall be defined. Make sure that the same type is used in the same array.
     + file: File type parameter, value is the file path in URI format.

   - **Unit**: Select the unit of measuring point data.

   - **Tags**: Tags can be used to describe a measuring point; depending on the needs, you can flexibly identify a measuring point, such as its type, group, and priority.

   - **Description**: A description of the measuring point.

2. Click **OK** to create the measuring point.

3. To create more measuring points, repeat the above steps.

### Adding Service

1. On the **Add Feature** pop-up window, select **Service** from the **Feature Type** drop-down list and complete the following settings:

   - **Name**: The name of the service; duplicate name is not allowed under the same module.
   - **Identifier**: A unique identification code for the service; duplicate identifier is not allowed under the same organization. This identifier value in JSON format is used as a key by the device to send attribute data. The cloud will verify the identifier before deciding whether to receive the data.
   - **Invoke Method**: The way to invoke the service.
     + Asynchronous: The cloud invokes the service and returns immediately; the reply message from the device is obtained asynchronously.
     + Synchronous: The cloud invokes the service and waits for the device to reply. If no reply is received, the invocation times out.
   - **Input Parameters**: Click **New Parameter** to configure the name, identifier, data type, and data unit of the input parameter of the service.
   - **Output Parameters**: Click **New Parameter** to configure the name, identifier, data type, and data unit of the output parameter of the service.
   - **Description**: A description of the service.

2. Click **OK** to create the service.

3. To create more services, repeat the above steps.

### Adding Event

1. On the **Add Feature** pop-up window, select **Event** from the **Feature Type** drop-down list and complete the following settings:

   - **Name**: The name of the event; duplicate name is not allowed under the same module.
   - **Identifier**: A unique identification code for the event; duplicate identifier is not allowed under the same organization. This identifier value in JSON format is used as a key by the device to send attribute data. The cloud will verify the identifier before deciding whether to receive the data.
   - **Severity**:
     + Info: Refers to general notifications reported by devices, such as a notification on job completion.
     + Warning: Warning messages. Emergencies or exceptions reported voluntarily by a running device; these events have a high priority. You can perform service logic processing and statistical analysis depending on the severity of the events.
     + Error: Error messages. Emergencies or exceptions reported voluntarily by a running device; these events have a high priority. You can perform service logic processing and statistical analysis depending on the severity of the events.
   - **Output Parameters**: Click **New Parameter** to configure the name, identifier, data type, and data unit of the output parameter.
   - **Description**: A description of the event.

2. Click **OK** to create the event.

## Step 3: (Optional) Adding a Tag

A tag describes the common information shared by models of the same type. It can be used to distinguish model objects by describing their categories such as domain, type, and scope. For example, by adding a **domain:solar** tag to a model, you are specifying that the model falls in the solar field.

1. From the list of created models, find the target model and click **Edit**.

2. Under the **Basic Information** tab, click **Edit** in the **Tags** section.

3. On the pop-up window, click **New tag**, and enter the key-value pair (key:value) for the new tag.

4. Click **OK** to save the tag.

## Results

When feature definition of the model is completed, the system will automatically generate a model for the product in JSON format.

## Related Information

- [Thing Model Overview](model_overview)
- [Creating a Product](../device/manage/creating_product)
