# Unit 2: Preparing the New Firmware File

In this unit, update the python program to include a new command for the LED light. Then, compress the program as a firmware file, which will be pushed from the EnOS cloud to the RPi for upgrade.

## Programming for the New Command

In [Unit 2](/docs/device-connection/en/2.0.9/tutorial/connecting_device_rasberrypi/registering_devices.html) of the *Connecting Raspberry Pi into EnOS* tutorial, we defined the *Light* measuring point for sending status control to the LED light, with the following performance:

- Sending 0 stands for light off
- Sending 1 stands for light on
- Sending 2 stands for light flickering (every 5 seconds)

The following code snippet shows how the LED light is controlled:

```
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
```

In this step, add 1 more status control *signal == 3* for the LED light, with the following performance:

- Sending 0 stands for light off
- Sending 1 stands for light on
- Sending 2 stands for light flickering every 5 seconds
- Sending 3 stands for light flickering every 2 seconds

Replace the above code in the python program with the following code snippet:

```
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
	elif signal == '1' or signal == '2' or signal == '3':
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
		elif signal == '3':
			current_sig = str(1 - int(get_light()))
			set_light(current_sig)
			time.sleep(2)
```

## Compressing the Program

Compress the program as the firmware file, which will be upload to EnOS Console for firmware upgrade OTA.

In this tutorial, name the firmware file as *firmware_2.0.zip*.

## Next Unit

[Uploading the Firmware File](uploading_firmware)

<!-- end -->
