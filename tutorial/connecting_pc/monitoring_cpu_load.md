# Unit 4: Monitoring CPU Load

By ingesting the PC system data with specified intervals, you can monitor the CPU load and report CPU load events if the CPU load exceeds the set threshold (for example, 50%).

In this unit, update the program that is used in [Unit 3](connecting_device) to add the function of monitoring CPU load.

1. Declare the function `monitor()` for monitoring the CPU load.

   ```
   public static void main(String[] args) throws Exception {
       connect();
       updateAttribute();
       monitor();
   }
   ```

2. Program the `monitor()` function for uploading the PC system data to EnOS Cloud by specified interval (for example, every 10 seconds) and monitoring the CPU load (for example, `cpu_load>0.2`). See the following code snippet:

   ```
       public static void monitor() throws Exception {
           long lastReportTs=0;
           while (true) {
               Map<String, Object> systemInfo= collectDeviceInfo();
               postMeasurepoint(systemInfo);

               double cpu_load= (double) systemInfo.get("cpu_used");
               if (cpu_load>0.2) {
                   long ts = System.currentTimeMillis();

                   if ((ts-lastReportTs)>(60*1000)) {
                       lastReportTs=ts;
                       reportCPULoadEvent(cpu_load, "[Warning] CPU load: "+ cpu_load);
                   }else{
                       System.out.println("[Warning] No reporting required, CPULoadEvent: " + cpu_load);
                   }
               }

               Thread.sleep(interval*1000);
           }
       }
   ```

3. Program the `reportCPULoadEvent()` function for reporting CPU load events if the CPU load exceeds the threshold. See the following code snippet:

   ```
       public static void reportCPULoadEvent(double value, String describe) {
           EventPostRequest request=EventPostRequest.builder()
                   .setQos(0)
                   .setEventIdentifier("cpu_event")
                   .addValue("value", value)
                   .addValue("message", describe)
                   .build();
           System.out.println(">>> Post Event: "+request);

           try {
               EventPostResponse resp = client.publish(request);
               System.out.println("<-- " + resp);
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   ```

4. Compile and run the program for device connection, data ingestion, and CPU load event reporting. See the follow example of the program code:

   ```
   import java.util.HashMap;
   import java.util.Map;

   import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
   import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
   import com.envisioniot.enos.iot_mqtt_sdk.core.exception.EnvisionException;
   import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.*;
   import oshi.hardware.HardwareAbstractionLayer;
   import oshi.software.os.OperatingSystem;

   public class Sample {

       private static final String uri = "tcp://{host}:{port}";
       private static final String productKey = "product_key";
       private static final String deviceKey = "device_key";
       private static final String deviceSecret = "device_secret";

       private static MqttClient client;
       private static int interval=5; // 10s

       public static void main(String[] args) throws Exception {
           connect();
           updateAttribute();
           monitor();

       }

       // Device connection initialization
       public static void connect() {
           System.out.println("start connect with callback ... ");
           try {
               client = new MqttClient(uri, productKey, deviceKey, deviceSecret);
               client.getProfile().setConnectionTimeout(60).setAutoReconnect(true);
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
               e.printStackTrace();
           }
           System.out.println("connect result :" + client.isConnected());
       }

       // Ingesting PC system and hardware data
       public static Map<String, Object> collectDeviceInfo() {
           oshi.SystemInfo si = new oshi.SystemInfo();
           HardwareAbstractionLayer hal = si.getHardware();
           OperatingSystem os = si.getOperatingSystem();

           Map<String, Object> data = new HashMap<String, Object>();
           data.put("system", os.toString());
           data.put("model", hal.getComputerSystem().getManufacturer() + " " + hal.getComputerSystem().getModel());
           data.put("cpu_core", hal.getProcessor().getLogicalProcessorCount());
           data.put("cpu_used", hal.getProcessor().getSystemCpuLoad());
           data.put("mem_total", hal.getMemory().getTotal());
           data.put("mem_used", hal.getMemory().getAvailable());
           data.put("cpu_used_average", hal.getProcessor().getSystemLoadAverage());
           data.put("cpu_temperature", hal.getSensors().getCpuTemperature());

           return data;
       }

       // Updating PC attributes with the ingested system and hardware data
       public static void updateAttribute(){
           Map<String, Object> deviceInfo= collectDeviceInfo();
           System.out.println("Computer info: "+deviceInfo);
           AttributeUpdateRequest request = AttributeUpdateRequest.builder()
                   .setQos(1)
                   .addAttribute("system", deviceInfo.get("system"))
                   .addAttribute("model", deviceInfo.get("model"))
                   .addAttribute("cpu_core", deviceInfo.get("cpu_core"))
                   .addAttribute("mem_total", deviceInfo.get("mem_total"))
                   .build();
           System.out.println(">>> Update Attribute: "+request);

           try {
               AttributeUpdateResponse resp = client.publish(request);
               System.out.println("<-- " + resp);
           } catch (Exception e) {
               e.printStackTrace();
           }
       }

       // Uploading PC system data into EnOS Cloud
       public static void postMeasurepoint(Map<String, Object> systemInfo) {
           MeasurepointPostRequest request = MeasurepointPostRequest.builder()
                   .setQos(0)
                   .addMeasurePoint("cpu_used", Double.parseDouble(systemInfo.get("cpu_used").toString())+0.0)
                   .addMeasurePoint("mem_used", systemInfo.get("mem_used"))
                   .build();
           System.out.println(">>> Post Measurepoint: "+request);

           try {
               MeasurepointPostResponse resp = client.publish(request);
               System.out.println("<-- " + resp);
           } catch (Exception e) {
               e.printStackTrace();
           }
       }

       // Monitoring the CPU load
       public static void monitor() throws Exception {
           long lastReportTs=0;
           while (true) {
               Map<String, Object> systemInfo= collectDeviceInfo();
               postMeasurepoint(systemInfo);

               double cpu_load= (double) systemInfo.get("cpu_used");
               if (cpu_load>0.2) {
                   long ts = System.currentTimeMillis();

                   if ((ts-lastReportTs)>(60*1000)) {
                       lastReportTs=ts;
                       reportCPULoadEvent(cpu_load, "[Warning] CPU load: "+ cpu_load);
                   }else{
                       System.out.println("[Warning] No reporting required, CPULoadEvent: " + cpu_load);
                   }
               }

               Thread.sleep(interval*1000);
           }
       }

       // Reporting CPU load events
       public static void reportCPULoadEvent(double value, String describe) {
           EventPostRequest request=EventPostRequest.builder()
                   .setQos(0)
                   .setEventIdentifier("cpu_event")
                   .addValue("value", value)
                   .addValue("message", describe)
                   .build();
           System.out.println(">>> Post Event: "+request);

           try {
               EventPostResponse resp = client.publish(request);
               System.out.println("<-- " + resp);
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   }
   ```

5. Check the running result of the program. The program will return the following result if the CPU load exceeds the threshold:

   ```
   >>> Post Event: AnswerableMessageBody{id='null', method='thing.event.cpu_event.post', version='null', params={time=1565852207454, events={message=[Warning] CPU load: 1.0, value=1.0}}}
   ```

6. Check the events that are posted to EnOS Console under the **Event** tab on the **Device Details** page.

   .. image:: media/uploaded_events.png

## Next Unit

[Controlling Data Upload Interval](controlling_upload_interval)
