# Unit 1: Developing OTA Capabilities on the Raspberry Pi

In [Unit 4](/docs/device-connection/en/latest/tutorial/connecting_device_rasberrypi/connecting_devices.html) of the *Connecting Raspberry Pi into EnOS* tutorial, we have developed a program using the EnOS Device SDK for MQTT for Python to connect the RPi to EnOS.

In this unit, upgrade the program to develop the OTA capabilities on the RPi, including the following functions:

- Reporting the firmware version
- Receiving the upgrade request pushed by EnOS
- Sending upgrade requests
- Reporting upgrade progress and the reasons for upgrade failure

For detailed information about firmware OTA upgrade modes and process, see [Firmware OTA Upgrade Overview](/docs/device-connection/en/latest/howto/ota/ota_overview.html).

## Prerequisites

The python program developed in [Unit 4](/docs/device-connection/en/latest/tutorial/connecting_device_rasberrypi/connecting_devices.html) of the *Connecting Raspberry Pi into EnOS* tutorial is running normally, and the LED light on the RPi can be controlled through the cloud.

## Programming for OTA Capabilities

Take the following steps to develop a program using the EnOS Device SDK for MQTT for Python, for empowering the RPi device with OTA capabilities:

1. Export the python program developed in [Unit 4](/docs/device-connection/en/latest/tutorial/connecting_device_rasberrypi/connecting_devices.html) of the *Connecting Raspberry Pi into EnOS* tutorial from the USB flash disk to your workstation.

2. Update the program with the following code snippet for the OTA service.

   ```
   def report_version(mqtt_client, version):
   	meapt_req = OtaVersionReportRequest.builder() \
   		.setProductKey(gateway_product_key).setDeviceKey(gateway_device_key) \
   		.setVersion(version) \
   		.build()
   	meapt_res = mqtt_client.publish(meapt_req)
   	if meapt_res:
   		print('OtaVersionReport_response: %s' % meapt_res.getCode())

   def report_progress(mqtt_client, progress, desc):
   	meapt_req = OtaProgressReportRequest.builder() \
   		.setProductKey(gateway_product_key).setDeviceKey(gateway_device_key) \
   		.setStep(progress) \
   		.setDesc(desc) \
   		.build()
   	meapt_res = mqtt_client.publish(meapt_req)
   	if meapt_res:
   		print('OtaProgressReport_response: %s' % meapt_res.getCode())

   def upgrade_firmware(arrivedMessage, replyHandler):
   	global exit_flag
   	OtaUpgradeCommand = arrivedMessage
   	firmware = OtaUpgradeCommand.getFirmwareInfo()
   	print(firmware.fileUrl, firmware.version)
   	if gen_url(firmware.fileUrl):
   		res = requests.get(api_url)
   		with open(cwd+'SampleLed_2.0.zip', 'wb') as f:
   			f.write(res.content)
   		print('downloading firmware finished')
   	else:
   		print('downloading firmware failed')
   		report_progress(client, '-2', 'downloading firmware failed')
   		return
   	if unzip(cwd+'SampleLed_2.0.zip'):
   		print('decompressing firmware finished')
   	else:
   		print('decompressing firmware failed')
   		report_progress(client, '-1', 'decompressing firmware failed')
   		return
   	if run_script():
   		print('running firmware finished')
   	else:
   		print('running firmware failed')
   		report_progress(client, '-4', 'running firmware failed')
   		return
   	exit_flag = True
   	os.remove(__file__)
   	os.remove(cwd+'SampleLed.log') if os.path.exists(cwd+'SampleLed.log') else None
   ```

3. Copy the python program to the USB flash disk and plug in it on RPi. Then copy the program to replace the old program in the RPi system.

   .. image:: media/copy_program.png

   The following sample program is for your reference:

   ```
   from core.MqttClient import MqttClient
   from message.downstream.ota.OtaUpgradeCommand import OtaUpgradeCommand
   from message.upstream.status.SubDeviceLoginRequest import SubDeviceLoginRequest
   from message.upstream.topo.TopoAddRequest import TopoAddRequest
   from message.upstream.topo.SubDeviceInfo import SubDeviceInfo
   from message.upstream.ota.OtaVersionReportRequest import OtaVersionReportRequest
   from message.upstream.ota.OtaProgressReportRequest import OtaProgressReportRequest
   from message.upstream.topo.TopoGetRequest import TopoGetRequest
   from message.upstream.topo.TopoDeleteRequest import TopoDeleteRequest
   from message.downstream.tsl.MeasurepointSetCommand import MeasurepointSetCommand
   from message.upstream.tsl.MeasurepointPostRequest import MeasurepointPostRequest

   import os
   import sys
   import time
   import traceback
   import subprocess
   import requests
   from zipfile import ZipFile
   from hashlib import sha1
   from led import set_light, get_light
   from dht11 import read_dht11_dat_ex, destroy
   from concurrent.futures import ThreadPoolExecutor
   from threading import current_thread

   cwd = '/home/pi/enos_device_demo/'

   enos_mqtt_url = "tcp://mqtt-{service_host}:{port}"

   # gateway parameters
   gateway_product_key = "NBXxeOUg"
   gateway_device_key = "bTI4gHRvMw"
   gateway_device_secret = "z8FwQzHg4DAOQQUfrpl4"

   # sub-device parameters
   sub_product_key = 'xxxx'
   sub_device_key = "xxxx"
   sub_device_secret = "xxxx"
   sub_device_asset_id = 'xxxx'

   def onOnline():
   	global connected
   	connected = True
   	login_sub_device(client)                        # login the sub-device
   	print 'connected... %s' % connected

   def onOffine():
   	print 'disconnected...'

   def get_topo(mqtt_client):
   	try:
   		topo_get_req = TopoGetRequest.builder().build()
   		topo_get_res = mqtt_client.publish(topo_get_req)
   		print 'topo_response: code: %s' % topo_get_res.getCode()
   		print topo_get_res.getData()
   	except Exception as e:
   		print e

   def add_topo(mqtt_client):
   	try:
   		topo_req = TopoAddRequest.builder().addSubDevice(SubDeviceInfo(sub_product_key, sub_device_key, sub_device_secret)).build()
   		topo_res = mqtt_client.publish(topo_req)
   		print 'topo_response: code: %s' % topo_res.getCode()
   		print 'topo_response: message: %s' % topo_res.getMessage()
   	except Exception as e:
   		print e

   def delete_topo(mqtt_client):
   	try:
   		topo_del_req = TopoDeleteRequest.builder().addSubDevice(sub_product_key, sub_device_key).build()
   		topo_del_res = mqtt_client.publish(topo_del_req)
   		print 'topo_delete_response: %s' % topo_del_res.getCode()
   	except Exception as e:
   		print e

   def login_sub_device(mqtt_client):
   	try:
   		login_req = SubDeviceLoginRequest.builder().setSubDeviceInfo(sub_product_key, sub_device_key, sub_device_secret).build()
   		login_res = mqtt_client.publish(login_req)
   		print 'login_response: code: %s' % login_res.getCode()
   		print 'login_response: message: %s' % login_res.getMessage()
   	except Exception as e:
   		print e

   # post measure points data via MQTT
   def post_measure_points(mqtt_client, timestamp):
   	try:
   		global last_light_control
   		current_light_sig = get_light() if not last_light_control else last_light_control[0]
   		data = read_dht11_dat_ex()
   		if isinstance(data, tuple):
   			humidity, temperature = data
   		else:
   			return
   		meapt_req = MeasurepointPostRequest.builder() \
   			.setProductKey(sub_product_key).setDeviceKey(sub_device_key) \
   			.addMeasurePoint('Temperature', temperature) \
   			.addMeasurePoint('Humidity', humidity) \
   			.addMeasurePoint('Light_Flicker', int(get_light())) \
   			.addMeasurePoint('Light_Status', int(current_light_sig)) \
   			.addMeasurePoint('Version', 1.0) \
   			.setTimestamp(timestamp) \
   			.build()
   		meapt_res = mqtt_client.publish(meapt_req)
   		print 'measurepointPost_response: %s' % meapt_res.getCode()
   	except Exception as e:
   		print e

   # handle the received downstream message and implement your logic
   def handle_msg(arrivedMessage, replyHandler):
   	'''
   	:param arrivedMessage: <attributes:deviceKey,prodectKey,id,messageTopic,method,params,version>
   	:param replyHandler: <method:replyWithPayload>
   	'''
   	# handle logic
   	global thread_id, last_light_control
   	thread_id = current_thread().ident
   	print(arrivedMessage.params)
   	signal = str(arrivedMessage.params.get('Light'))
   	last_light_control.pop() if last_light_control else None
   	last_light_control.append(signal)
   	if signal == '0':
   		set_light(signal)
   		replyHandler.reply_with_payload(code=200, message='success', data=thread_id)
   	elif signal == '1' or signal == '2':
   		executor.submit(light_thread, signal, thread_id)
   		replyHandler.reply_with_payload(code=200, message='success', data=thread_id)

   def light_thread(signal, threadID):
   	while threadID == thread_id:
   		if signal == '1':
   			state = int(get_light())
   			set_light(signal) if state == 0 else None
   			time.sleep(0.1)
   		elif signal == '2':
   			current_sig = str(1 - int(get_light()))
   			set_light(current_sig)
   			time.sleep(5)

   def report_version(mqtt_client, version):
   	meapt_req = OtaVersionReportRequest.builder() \
   		.setProductKey(gateway_product_key).setDeviceKey(gateway_device_key) \
   		.setVersion(version) \
   		.build()
   	meapt_res = mqtt_client.publish(meapt_req)
   	if meapt_res:
   		print('OtaVersionReport_response: %s' % meapt_res.getCode())

   def report_progress(mqtt_client, progress, desc):
   	meapt_req = OtaProgressReportRequest.builder() \
   		.setProductKey(gateway_product_key).setDeviceKey(gateway_device_key) \
   		.setStep(progress) \
   		.setDesc(desc) \
   		.build()
   	meapt_res = mqtt_client.publish(meapt_req)
   	if meapt_res:
   		print('OtaProgressReport_response: %s' % meapt_res.getCode())

   def upgrade_firmware(arrivedMessage, replyHandler):
   	global exit_flag
   	OtaUpgradeCommand = arrivedMessage
   	firmware = OtaUpgradeCommand.getFirmwareInfo()
   	print(firmware.fileUrl, firmware.version)
   	if gen_url(firmware.fileUrl):
   		res = requests.get(api_url)
   		with open(cwd+'SampleLed_2.0.zip', 'wb') as f:
   			f.write(res.content)
   		print('downloading firmware finished')
   	else:
   		print('downloading firmware failed')
   		report_progress(client, '-2', 'downloading firmware failed')
   		return
   	if unzip(cwd+'SampleLed_2.0.zip'):
   		print('decompressing firmware finished')
   	else:
   		print('decompressing firmware failed')
   		report_progress(client, '-1', 'decompressing firmware failed')
   		return
   	if run_script():
   		print('running firmware finished')
   	else:
   		print('running firmware failed')
   		report_progress(client, '-4', 'running firmware failed')
   		return
   	exit_flag = True
   	os.remove(__file__)
   	os.remove(cwd+'SampleLed.log') if os.path.exists(cwd+'SampleLed.log') else None

   def unzip(zip_path):
   	try:
   		folder = os.path.split(zip_path)[0]
   		zip_file = ZipFile(zip_path)
   		file = zip_file.namelist()[0]
   		zip_file.extract(file, folder)
   		zip_file.close()
   		os.remove(zip_path)
   		return True
   	except:
   		print(traceback.format_exc())
   		return

   def run_script():
   	cmd = 'nohup python %s >> %s 2>&1 &' % (cwd+'SampleLed_2.0.py', cwd+'SampleLed_2.0.log')
   	try:
   		subprocess.Popen(cmd, stderr=subprocess.STDOUT, shell=True)
   	except subprocess.CalledProcessError as e:
   		print(e.output.decode('utf8'))
   		return
   	else:
   		return True

   def gen_url(url):
   	global api_url
   	params = {"path" : url}
   	time_stamp = str(int(time.time() * 1000))
   	params["requestTimestamp"] = time_stamp
   	params["orgId"] = org_id

   	sign_str = access_key
   	keys = sorted(params.keys())
   	for key in keys:
   		sign_str += key + str(params[key])
   	sign_str += secret_key
   	psw = sha1()
   	psw.update(sign_str.encode('utf8'))
   	sign = psw.hexdigest().upper()
   	api_url = api_url.format(url, time_stamp, sign)
   	print api_url
   	return True

   if __name__ == "__main__":
   	try:
   		print 'main begin...'
   		client = MqttClient(enos_mqtt_url, gateway_product_key, gateway_device_key, gateway_device_secret)
   		client.getProfile().setAutoReconnect(True)
   		client.setupFileLogger(cwd + 'log.json')              # set the log configuration in the SDK
   		client.onOnline = onOnline
   		client.onOffline = onOffine
   		print 'try to connect'
   		connected = False
   		connect_cnt = 0
   		while connected is False:
   			client.connect()                                # connect in sync
   			print 'connecte status %s' % connected
   			connect_cnt += 1
   			print 'connect for %d time' % connect_cnt
   			print time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(time.time()))
   			time.sleep(10)
   		print 'connect finished'

   		thread_id = None
   		last_light_control = []
   		executor = ThreadPoolExecutor(1)
   		client.onMessage(MeasurepointSetCommand().getClass(), handle_msg)  # register the handle_msg
   		client.onMessage(OtaUpgradeCommand().getClass(), upgrade_firmware)
   		add_topo(client)                                # add the device to the gateway as sub-device
   		report_version(client, '1.0')
   		exit_flag = False
   		cnt = 0
   		interval = 5
   		while True:
   			if exit_flag:
   				destroy()
   				sys.exit(0)
   			timestamp = int(time.time() * 1000)  # timestamp in milliseconds
   			post_measure_points(client, timestamp)     # publish measure points data
   			cnt = cnt + 1
   			time.sleep(interval)
   	except:
   		destroy()
   ```

4. Run the updated program in the Terminal with the following command:

   ```
   python /home/pi/Desktop/SampleLed.py
   ```

5. Check the running status of the program. The program will report the current firmware version to the cloud, with a message like this:

   ```
   OtaProgressReport_response: {id='null', method='ota.device.inform', version='null', params={version=1.0}}
   ```

.. note:: From the response, we know that the current firmware version of the device is 1.0.   

## Next Unit

[Preparing the New Firmware File](preparing_firmware)

<!--end-->
