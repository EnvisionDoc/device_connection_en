# Managing Models

In **Model**, click **Edit** for the private model you choose. Go to **Feature Definition** , use the **Export Model** , **Import Model** buttons to manage your existing model. You can download and edit existing model elements (attributes, measuring points, events, services) and then import the edited model onto EnOS.

## About This Task

After creating a model, you may have to change its elements or add new elements as required. This task helps you manage existing models by using EnOS-defined JSON files or Excel files.

## Before You Start

You need to have proper authority to view and manage models. Contact your OU administrator for access. For more information, see [Policy, Role, and Access](/docs/iam/en/latest/access_policy).

## Procedure

To edit your existing model, export the model first, then edit the downloaded file and import it to replace the existing model. 

### Exporting 

1. Go to **Model**, click **Edit** for the model you choose.

2. Go to **Feature Definition**, click **Export Model**. Select the format of the file you want to download: JSON or Excel.

3. Edit the downloaded file.

  The downloaded JSON file is named *model_Modelname_Timestamp.json*, where `Modelname` is the **Model Name** field from EnOS, and `Timestamp` is the time you download this JSON file in *hhmmssSS*, SS indicating millisecond.

  The downloaded Excel file is named **ThingModel_Modelname.xlsx** where `Modelname` is the **Model Name** field from EnOS.

  Edit the file to modify existing model elements or add new elements.

### Importing

1. On **Feature Definition**, click **Import Model** .

2. In the pop-up, click **Upload**. Select the local JSON file.

3. Click **Import** if your uploaded file passes format verification.
 
 You may have to correct format issues before you can complete importing. Refer to the error messages prompted after you have uploaded the JSON file.

## Results

The model you selected has been changed based on the imported file you edited.

