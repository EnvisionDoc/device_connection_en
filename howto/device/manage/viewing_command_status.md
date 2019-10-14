# Issuing Cached Commands from Cloud

EnOS can immediately send commands to devices or cache the commands until later at the request of an application.

- Instant commands: EnOS immediately sends the command once it receives it, and if the device is offline or the device does not receive the command, the command failed to be sent.
- Cached commands: EnOS first adds the command to a queue. After the device re-establishes connection with EnOS, EnOS sends the cached commands in order. You can use command caching where the device is disconnected from EnOS, or the low-power devices might fail to receive and execute the commands sent by EnOS in time as they are usually sleeping. EnOS can cache up to 50 commands for a single device.

When an application sends a request to EnOS, it can carry the parameter `pendingTtl`. This parameter indicates for how long EnOS caches this command in the queue. Its value ranges from 0 second to 72800 seconds, i.e. 0-48 hours. The default value of `pendingTtl` is 0, that is, the command will be sent immediately, if `pendingTtl` is not specified in the request.

`pendingTtl` = 0: The command is sent immediately.

`pendingTtl` > 0: The command is cached until later.


## Sending Cached Commands

How a cached command is sent is illustrated in the following figure, assuming `pendingTtl`=3600:

.. image:: ../../../media/issuing_cached_commands.png

The following rules apply when EnOS sends a cached command to a device:

- **Sequential sending**: Only after the device have processed and returned the response of the previous command will EnOS send the next cached command. The responses for the previous command include: "succeeded" (the device receives the command and responds in time) and "timed out" (EnOS has sent the command but the device does not respond in time);
- **No priority**: If an application requests EnOS to send a new command before EnOS is able to send the commands previously cached, whether `pendingTtl` of the new command being 0 or a non-zero value, this new command goes into the queue and waits for its turn to be sent. This rule applies regardless of the device being online or offline. If the new command needs to be sent immediately, the application should call the `cancelOneCommand` and `cancelAllCommand` APIs to revoke the cached command and then sends the new command. In other words, the commands that should be delivered immediately can also be stored in a queue to be sent in order. If the device is offline, the command that is sent immediately fails to be sent.
- **Cleared after expiry**: EnOS clears any expired cached command from the queue and sets its status to "Expired";

## Development on Device

A device can include customized descriptive message in the response to indicate the status of command execution. The response format is given as follows, where `message` is the key used for describing the command execution status.

```json
{
    "id": "123",
    "code": 200,
    "message": "Succeeded"               
    "data": {                                
               "outputdata1":234,
               "outputdata2":"xyz"
              }
}
```

## Check the status of cached commands

When a command is cached, you can check its status in the console.

## Before You Start

- Obtain the permissions for device management; if not, ask your OU administrator to grant such permissions. See [Policies, Roles and Permissions](/docs/iam/en/2.0.9/access_policy).

## Steps

1. Select **Device Management > Device** in the console.

2. Select the device for which you want to check the command and click the Check icon in the **Operations** column.

3. On the **Device Details** page, click the **Commands** tab to check all currently cached commands for the selected device.

 .. image:: ../../../media/cached_commands.png

 You can perform the following actions on this page:
     - Filter the commands to be checked
     - For commands that have been **created** but not **Sent** , you can make the applicationcall `cancelOneCommand` and `cancelAllCommand` to revoke them.

  You can query the information about historical commands within the last 7 days at most, including their request id, command name, command content, response, command creation time, status information, and so on.

## Results

A command has the following statuses:

- **Created**: EnOS received request and have created a command, but has not sent it to the device.
- **Revoked**: The command has been revoked. Only the commands that are **Created ** can be revoked.
- **Expired**: The command expires beyond the specified validity period. The validity period can be specified by the application. 
- **Sent**: EnOS delivers the command to the downstream message channel towards the device, but the device has not responded yet. Note that this status does not always indicate successful sending;
- **Succeeded**: The device receives the command and returns a response message indicating that the device has successfully executed the command. The device can include customized descriptive message (optional) in `message` in the response.
- **Failed**: The command fails to be sent due to various reasons: such as the command could not be delivered correctly to the downstream message channel, or the device receives the command but the execution fails. Possible causes include offline device, parsing script error, invalid command, etc.
- **Timed out**: The command is sent to the downstream message channel and the device does not respond within the timeout period.

## Related documents

Click EnOS API > API Documentation in the EnOS console to check how to use the APIs.
