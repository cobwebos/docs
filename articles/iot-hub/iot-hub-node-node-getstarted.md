<properties
	pageTitle="适用于 Node.js 的 Azure IoT 中心入门 | Azure"
	description="适用于 Node.js 的 Azure IoT 中心入门教程。配合 Azure IoT SDK 使用 Azure IoT 中心和 Node.js 来实施物联网解决方案。"
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.date="03/22/2016"
     wacn.date=""/>

# 适用于 Node.js 的 Azure IoT 中心入门

[AZURE.INCLUDE [iot-hub-selector-get-started](../includes/iot-hub-selector-get-started.md)]

## 介绍

Azure IoT 中心是一项完全托管的服务，可在数百万个物联网 (IoT) 设备和一个解决方案后端之间实现安全可靠的双向通信。IoT 项目面临的最大挑战之一是如何可靠且安全地将设备连接到解决方案后端。为了解决此难题，IoT 中心：

- 提供可靠的设备到云和云到设备的超大规模消息传送。
- 使用每个设备的安全凭据和访问控制来实现安全通信。
- 包含最流行语言和平台的设备库。

本教程演示如何：

- 使用 Azure 门户创建 IoT 中心。
- 在 IoT 中心内创建设备标识。
- 创建一个模拟设备用于将遥测数据发送到云后端。

本教程最后提供了三个 Node.js 控制台应用程序：

* **CreateDeviceIdentity.js**，用于创建设备标识和关联的安全密钥以连接模拟设备。
* **ReadDEviceToCloudMessages.js**，显示模拟设备发送的遥测数据。
* **SimulatedDevice.js**，它使用前面创建的设备标识连接到 IoT 中心，并使用 AMQPS 协议每秒发送遥测消息一次。

> [AZURE.NOTE] [IoT 中心 SDK][lnk-hub-sdks] 一文提供了有关多种 SDK 的信息，你可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序。

若要完成本教程，你需要以下各项：

+ Node.js 版本 0.12.x 或更高版本。<br/>[准备开发环境][lnk-dev-setup]介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。

+ 有效的 Azure 帐户。<br/>如果你没有帐户，可以创建一个试用帐户，只需几分钟即可完成。有关详细信息，请参阅 [Azure 免费试用][lnk-free-trial]。

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../includes/iot-hub-get-started-create-hub.md)]

最后一步是在 IoT 中心边栏选项卡上单击“设置”，然后在“设置”边栏选项卡上单击“消息传送”。在“消息传送”边栏选项卡上，记下“与事件中心兼容的名称”和“与事件中心的兼容终结点”。创建 **read-d2c-messages** 应用程序时，将要用到这些值。

    ![][6]

现在，你已创建 IoT 中心，并获得了完成本教程余下部分所需的 IoT 中心主机名、IoT 中心连接字符串、与事件中心兼容的名称，以及与事件中心兼容的终结点值。

## 创建设备标识

在本部分中，你将创建一个 Node.js 控制台应用程序，用于在 IoT 中心的标识注册表中创建新的设备标识。设备无法连接到 IoT 中心，除非它在设备标识注册表中具有条目。有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]的“设备标识注册表”部分。当你运行此控制台应用程序时，它将生成唯一的设备 ID 和密钥，当设备向 IoT 中心发送设备到云的消息时，可以用于标识设备本身。

1. 新建名为 **createdeviceidentity** 的空文件夹。在 **createdeviceidentity** 文件夹中，于命令提示符下使用以下命令创建新的 package.json 文件。接受所有默认值：

    ```
    npm init
    ```

2. 在命令提示符下的 **createdeviceidentity** 文件夹中运行以下命令安装 **azure-iothub** 包：

    ```
    npm install azure-iothub --save
    ```

3. 使用文本编辑器在 **createdeviceidentity** 文件夹中创建一个新的 **CreateDeviceIdentity.js** 文件。

4. 在 **CreateDeviceIdentity.js** 文件的开头添加以下 `require` 语句：

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. 将以下代码添加到 **CreateDeviceIdentity.js** 文件，并将占位符值替换为在上一节中为 IoT 中心创建的连接字符串：

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 添加以下代码，以便在 IoT 中心的设备标识注册表中创建新的设备定义。如果该设备 ID 在注册表中不存在，此代码将创建一个新的设备，否则将返回现有设备的密钥：

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. 保存并关闭 **CreateDeviceIdentity.js** 文件。

8. 若要运行 **createdeviceidentity** 应用程序，请在命令提示符下于 createdeviceidentity 文件夹中执行以下命令：

    ```
    node CreateDeviceIdentity.js 
    ```

9. 记下**设备 ID** 和**设备密钥**。稍后在创建连接到作为设备的 IoT 中心的应用程序时需要这些数据。

> [AZURE.NOTE] IoT 中心标识注册表只存储设备标识，以启用对中心的安全访问。它存储设备 ID 和密钥作为安全凭据，以及启用或禁用标志让你禁用对单个设备的访问。如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。

## 接收设备到云的消息

在本部分中，你将创建一个 Node.js 控制台应用程序，用于读取来自 IoT 中心的设备到云消息。IoT 中心公开与[事件中心][lnk-event-hubs-overview]兼容的终结点，以让你读取设备到云的消息。为了简单起见，本教程创建的基本读取器不适用于高吞吐量部署。[处理设备到云的消息][lnk-process-d2c-tutorial]教程介绍了如何大规模处理设备到云的消息。[事件中心入门][lnk-eventhubs-tutorial]教程更详细介绍了如何处理来自事件中心的消息，此教程也适用于与 IoT 中心事件中心兼容的终结点。

> [AZURE.NOTE] 读取设备到云消息的事件中心兼容终结点始终使用 AMQPS 协议。

1. 新建名为 **readdevicetocloudmessages** 的空文件夹。在命令提示符下的 **readdevicetocloudmessages** 文件夹中使用以下命令创建新的 package.json 文件。接受所有默认值：

    ```
    npm init
    ```

2. 在命令提示符下的 **readdevicetocloudmessages** 文件夹中运行以下命令以安装 **amqp10** 和 **bluebird** 包：

    ```
    npm install amqp10 bluebird --save
    ```

3. 使用文本编辑器在 **readdevicetocloudmessages** 文件夹中创建一个新的 **ReadDeviceToCloudMessages.js** 文件。

4. 在 **ReadDeviceToCloudMessages.js** 文件的开头添加以下 `require` 语句：

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. 添加以下变量声明，并将占位符替换为前面记下的值。**{your event hub-compatible namespace}** 占位符的值来自门户中的**与事件中心兼容的终结点**字段，其格式为 **namespace.servicebus.windows.net**（没有 sb:// 前缀）。

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] 此代码假设已在 F1（免费）层创建 IoT 中心。免费 IoT 中心有“0”和“1”这两个分区。如果使用另一种定价层创建 IoT 中心，则应调整代码，以便为每个分区创建 **MessageReceiver**。

6. 添加以下筛选器定义。在创建开始运行后只读取发送到 IoT 中心的消息的接收方时，此应用程序将使用筛选器。这很适合测试环境，因为这样可以看到当前的消息集，但在生产环境中，代码应该要确保它能处理所有消息。有关详细信息，请参阅[如何处理 IoT 中心设备到云的消息][lnk-process-d2c-tutorial]教程。

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. 添加以下代码以创建接收地址和 AMQP 客户端：

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. 添加以下两个函数用于在控制台中列显输出：

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. 添加以下函数，充当使用筛选器的给定分区的接收方：

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. 添加以下代码，以连接到事件中心兼容的终结点并启动接收方：

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. 保存并关闭 **ReadDeviceToCloudMessages.js** 文件。

## 创建模拟设备应用程序

在本部分中，你将创建一个 Node.js 控制台应用程序，用于模拟向 IoT 中心发送设备到云消息的设备。

1. 新建名为 **simulateddevice** 的空文件夹。在命令提示符下的 **simulateddevice** 文件夹中使用以下命令创建新的 package.json 文件。接受所有默认值：

    ```
    npm init
    ```

2. 在命令提示符下的 **simulateddevice** 文件夹中运行以下命令安装 **azure-iot-device-amqp** 包：

    ```
    npm install azure-iot-device-amqp --save
    ```

3. 使用文本编辑器在 **simulateddevice** 文件夹中创建一个新的 **SimulatedDevice.js** 文件。

4. 在 **SimulatedDevice.js** 文件的开头添加以下 `require` 语句：

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 添加 **connectionString** 变量并使用它来创建设备客户端。将 **{youriothubname}** 替换为你的 IoT 中心名称，将 **{yourdeviceid}** 和 **{yourdevicekey}** 替换为创建设备标识部分中生成的设备值：

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. 添加以下函数以显示应用程序的输出：

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 创建回调并使用 **setInterval** 函数每隔一秒将新消息发送到 IoT 中心：

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. 与 IoT 中心建立连接并开始发送消息：

    ```
    client.open(connectCallback);
    ```

9. 保存并关闭 **SimulatedDevice.js** 文件。

> [AZURE.NOTE] 为简单起见，本教程不实现任何重试策略。在生产代码中，你应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。


## 运行应用程序

现在，你已准备就绪，可以运行应用程序了。

1. 在命令提示符下的 **readdevicetocloudmessages** 文件夹中运行以下命令开始监视 IoT 中心：

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. 在命令提示符下的 **simulateddevice** 文件夹中运行以下命令开始将遥测数据发送到 IoT 中心：

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure 门户][lnk-portal]中的“使用情况”磁贴显示了发送到中心的消息数：

    ![][43]

## 后续步骤

在本教程中，你已在门户中配置了新的 IoT 中心，然后在中心的标识注册表中创建了设备标识。你已使用此设备标识来让模拟设备应用向中心发送设备到云的消息，并创建了用于显示中心所接收消息的应用。可以使用以下教程继续探索 IoT 中心功能和其他 IoT 方案：

- [使用 IoT 中心发送云到设备的消息][lnk-c2d-tutorial]介绍了如何将消息发送到设备，并处理 IoT 中心生成的传送反馈。
- [处理设备到云的消息][lnk-process-d2c-tutorial]介绍了如何可靠地处理来自设备的遥测数据和交互消息。
- [从设备上载文件][lnk-upload-tutorial]介绍了一种模式，该模式利用云到设备的消息来帮助从设备上载文件。

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/zh-cn/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: /documentation/articles/event-hubs-csharp-ephcs-getstarted
[lnk-devguide-identity]: /documentation/articles/iot-hub-devguide/#identityregistry
[lnk-event-hubs-overview]: /documentation/articles/event-hubs-overview

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: /documentation/articles/iot-hub-csharp-csharp-c2d
[lnk-process-d2c-tutorial]: /documentation/articles/iot-hub-csharp-csharp-process-d2c
[lnk-upload-tutorial]: /documentation/articles/iot-hub-csharp-csharp-file-upload

[lnk-hub-sdks]: /documentation/articles/iot-hub-sdks-summary
[lnk-free-trial]: /pricing/1rmb-trial/
[lnk-resource-groups]: /documentation/articles/resource-group-portal
[lnk-portal]: https://manage.windowsazure.cn




<!---HONumber=Mooncake_0523_2016-->