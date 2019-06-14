---
title: 了解 Azure IoT 中心 AMQP 支持 | Microsoft Docs
description: 开发人员指南-支持设备连接到 IoT 中心使用 AMQP 协议的面向设备和面向服务的终结点。 包含有关 Azure IoT 设备 SDK 中内置 AMQP 支持的信息。
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: c304c9b7fe02e3396d49aee0b70576071d9fac92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055378"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>使用 AMQP 协议来与 IoT 中心通信

Azure IoT 中心支持[OASIS 高级消息队列协议 (AMQP) 1.0 版](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)提供各种功能通过面向设备和面向服务的终结点。 本文档介绍如何使用 AMQP 客户端连接到 IoT 中心使用 IoT 中心功能。

## <a name="service-client"></a>服务客户端

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>连接并向 IoT 中心 （服务客户端） 进行身份验证
若要使用 AMQP 连接到 IoT 中心，客户端可以使用[基于声明的安全 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或[简单身份验证和安全层 (SASL) 身份验证](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)。

服务客户端需要以下信息：

| 信息 | 值 | 
|-------------|--------------|
| IoT 中心主机名 | `<iot-hub-name>.azure-devices.net` |
| 密钥名称 | `service` |
| 访问密钥 | 与服务相关联的主密钥或辅助密钥 |
| 共享访问签名 | 采用以下格式的生存期较短的共享的访问签名： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 若要获取用于生成此签名的代码，请参阅[控制对 IoT 中心的访问](./iot-hub-devguide-security.md#security-token-structure)。

下面的代码片段使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)连接到 IoT 中心通过在发送方链接。

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>调用云到设备消息 （服务客户端）
若要了解有关服务和 IoT 中心之间以及设备与 IoT 中心之间的云到设备消息交换信息，请参阅[从 IoT 中心发送云到设备消息](iot-hub-devguide-messages-c2d.md)。 服务客户端使用两个链接来发送消息和以前接收的反馈将消息从发送设备，如下表中所述：

| 创建者 | 链接类型 | 链接路径 | 描述 |
|------------|-----------|-----------|-------------|
| 服务 | 发送方链接 | `/messages/devicebound` | 适用于设备要发送云到设备消息发送到此链接服务。 通过此链接发送的消息具有其`To`属性设置为目标设备的接收方链接路径， `/devices/<deviceID>/messages/devicebound`。 |
| 服务 | 接收方链接 | `/messages/serviceBound/feedback` | 继续往下看从设备接收到此链接服务的完成、 拒绝和用户反馈消息。 有关反馈消息的详细信息，请参阅[从 IoT 中心发送云到设备消息](./iot-hub-devguide-messages-c2d.md#message-feedback)。 |

下面的代码段演示如何创建云设备的消息并将其发送到设备，通过使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)。

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

若要接收反馈，服务客户端，请创建一个接收方链接。 下面的代码段演示如何通过使用创建的链接[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

云到设备反馈消息中前面的代码所示，有的一个内容类型*application/vnd.microsoft.iothub.feedback.json*。 可以使用消息的 JSON 正文中的属性推断原始消息的传递状态：
* 密钥`statusCode`的反馈中正文具有以下值之一：*成功*，*过期*， *DeliveryCountExceeded*，*拒绝*，或*清除*。
* 密钥`deviceId`正文的反馈中具有目标设备的 ID。
* 密钥`originalMessageId`正文的反馈中具有原始服务发送的设备到云消息的 ID。 您可以使用此传递状态关联云到设备消息的反馈。

### <a name="receive-telemetry-messages-service-client"></a>接收遥测消息（服务客户端）

默认情况下，IoT 中心内置事件中心内存储引入的设备的遥测消息数。 服务客户端可以使用 AMQP 协议接收存储的事件。

为此，服务客户端首先需要连接到 IoT 中心终结点，并接收重定向到内置事件中心的地址。 服务客户端然后使用所提供的地址连接到内置事件中心。

在每个步骤中，客户端都需要提供以下信息片段：
* 有效的服务凭据 （服务共享的访问签名令牌）。
* 它想要从中检索邮件的使用者组分区格式正确的路径。 对于给定的使用者组和分区 ID，该路径采用以下格式：`/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>`（默认的使用者组为 `$Default`）。
* 若要指定分区中的起始点可选筛选器谓词。 序列号、 偏移量或排入队列的时间戳的形式可以是此谓词。

下面的代码片段使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)来演示上述步骤：

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

对于一个给定的设备 ID 和 IoT 中心使用设备 ID 的哈希来确定哪些分区来存储其消息。 前面的代码段演示了如何从单个接收事件这类分区。 但是，请注意，典型的应用程序通常需要以检索存储在所有事件中心分区中的事件。


## <a name="device-client"></a>设备客户端

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>连接并向 IoT 中心 （设备客户端） 进行身份验证
若要使用 AMQP 连接到 IoT 中心，设备可以使用[基于声明的安全 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或[简单身份验证和安全层 (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)身份验证。

设备客户端需要以下信息：

| 信息 | 值 | 
|-------------|--------------|
| IoT 中心主机名 | `<iot-hub-name>.azure-devices.net` |
| 访问密钥 | 与设备相关联的主密钥或辅助密钥 |
| 共享访问签名 | 采用以下格式的生存期较短的共享的访问签名： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 若要获取用于生成此签名的代码，请参阅[控制对 IoT 中心的访问](./iot-hub-devguide-security.md#security-token-structure)。


下面的代码片段使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)连接到 IoT 中心通过在发送方链接。

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

在设备操作中支持使用以下链接路径：

| 创建者 | 链接类型 | 链接路径 | 描述 |
|------------|-----------|-----------|-------------|
| 设备 | 接收方链接 | `/devices/<deviceID>/messages/devicebound` | 云到设备消息与发往设备的设备所接收此链接的每个目标。 |
| 设备 | 发送方链接 | `/devices/<deviceID>messages/events` | 通过此链接发送从设备发送设备到云消息。 |
| 设备 | 发送方链接 | `/messages/serviceBound/feedback` | 通过设备通过此链接发送到服务的云到设备消息反馈。 |


### <a name="receive-cloud-to-device-commands-device-client"></a>接收云到设备命令 （设备客户端）
发送到设备的云到设备命令到达`/devices/<deviceID>/messages/devicebound`链接。 设备可以分批接收这些消息，并根据需要在消息中使用消息数据有效负载、消息属性、批注或应用程序属性。

下面的代码片段使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)) 按设备接收设备到云消息。

```python
# ... 
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>发送遥测消息（设备客户端）
此外可以通过使用 AMQP，从设备中发送的遥测消息数。 设备可以选择提供应用程序属性的字典或各种消息属性，例如消息 id。

下面的代码片段使用[uAMQP 库在 Python 中的](https://github.com/Azure/azure-uamqp-python)若要从设备发送设备到云的消息。


```python
# ... 
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>附加说明
* AMQP 连接可能中断网络故障或由于过期的身份验证令牌 （在代码中生成）。 服务客户端必须处理这些情况下并重新建立连接和链接，如果需要。 如果身份验证令牌过期，客户端可以通过主动续订过期日期前的标记来避免连接下拉。
* 偶尔，您的客户端必须能够正确地处理链接重定向。 若要了解此类操作，请参阅 AMQP 客户端文档。

## <a name="next-steps"></a>后续步骤

若要了解有关 AMQP 协议的详细信息，请参阅[AMQP 1.0 版规范](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)。

若要详细了解 IoT 中心消息传递，请参阅：

* [云到设备的消息](./iot-hub-devguide-messages-c2d.md)
* [对其他协议的支持](iot-hub-protocol-gateway.md)
* [对消息队列遥测传输 (MQTT) 协议的支持](./iot-hub-mqtt-support.md)
