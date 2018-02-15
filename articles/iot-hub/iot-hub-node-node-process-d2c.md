---
title: "使用 Azure IoT 中心路由消息 (Node) | Microsoft Docs"
description: "如何使用路由规则和自定义终结点将消息发送到其他后端服务，从而处理 Azure IoT 中心的设备到云消息。"
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: f314d24250330a4dadf99d98b94c98b3db03f22c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>使用 IoT 中心路由消息 (Node)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教程是在 [IoT 中心入门]教程的基础上制作的。  本教程：

* 介绍如何以基于配置的轻松方式，使用路由规则发送设备到云的消息。
* 介绍如何隔离需要解决方案后端立即执行操作以进行进一步处理的交互式消息。  例如，设备可能将发送一条警报消息，触发在 CRM 系统中插入票证。  与此相反，数据点消息（例如温度遥测）则送入分析引擎。

本教程结束时，可运行三个 Node.js 控制台应用：

* **SimulatedDevice.js**，（在 [IoT 中心入门]教程中创建的应用的修改版本）每秒发送一次设备到云的数据点消息，根据随机时间间隔发送设备到云的交互式消息。 此应用使用 MQTT 协议来与 IoT 中心通信。
* **ReadDeviceToCloudMessages.js**，显示设备应用发送的遥测数据。
* **ReadCriticalQueue.js** 从附加到 IoT 中心的服务总线队列中取消关键消息的排队。

> [!NOTE]
> IoT 中心对许多设备平台和语言（包括 C、Java 和 JavaScript）提供 SDK 支持。 若要了解如何将本教程中的设备替换为物理设备，以及如何将设备连接到 IoT 中心，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* [IoT 中心入门]教程的完整工作版本。
* Node.js 版本 4.0.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

我们建议另外阅读有关 [Azure 存储]和 [Azure 服务总线]的主题。

## <a name="send-interactive-messages-from-a-device-app"></a>从设备应用发送交互式消息
在本部分中，会修改在 [IoT 中心入门]教程中创建的设备应用，不定期发送需要立即处理的消息。

1. 使用文本编辑器打开 **simulateddevice\SimulatedDevice.js** 文件。 此文件包含 [IoT 中心入门]教程中创建的 **SimulatedDevice** 应用的代码。

2. 将 **connectCallback** 函数替换为以下代码：

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    此方法会将 `"level": "critical"` 和 `"level": "storage"` 属性随机添加到设备发送的消息，此设备模拟需要应用程序后端立即执行操作的消息，或需要永久存储的消息。 应用程序支持基于消息正文的路由消息。
   
   > [!NOTE]
   > 可使用消息属性根据各种方案路由消息，包括冷路径处理和此处所示的热路径示例。

2. 保存并关闭 **simulateddevice\SimulatedDevice.js** 文件。

    > [!NOTE]
    > 我们强烈建议根据 MSDN 文章 [暂时性故障处理]中的建议实施重试策略（如指数退让）。

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>向 IoT 中心添加服务总线队列并向其路由消息

在本部分中，将创建一个服务总线队列并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息上的现有属性发送消息到队列。 若要深入了解如何处理来自服务总线队列的消息，请参阅[队列入门][lnk-sb-queues-node]教程。

1. 按[队列入门][lnk-sb-queues-node]中所述，创建服务总线队列。 记下命名空间和队列名称。

    > [!NOTE]
    > 用作 IoT 中心终结点的服务总线队列和主题不得启用会话或重复检测选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为“无法访问”。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

    ![IoT 中心的终结点][30]

3. 在“终结点”边栏选项卡中，单击顶部的“添加”，将队列添加到 IoT 中心。 将终结点命名为“CriticalQueue”，并使用下拉列表选择“服务总线队列”、队列所在的服务总线命名空间和队列名称。 完成后，单击底部的“确定”。  

    ![添加终结点][31]

4. 现在单击 IoT 中心的“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“设备消息”作为数据源。 输入 `level="critical"` 作为条件，并选择自定义终结点 **CriticalQueue** 作为路由规则终结点。 在底部单击“保存”。  

    ![添加路由][32]

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

    ![回退路由][33]

## <a name="optional-read-from-the-queue-endpoint"></a>（可选）从队列终结点读取

在本部分创建一个 Node.js 控制台应用，用于读取来自 IoT 服务总线的关键消息。 请参阅[队列入门][lnk-sb-queues-node]中的更多信息。 

1. 创建名为 `readcriticalqueue` 的空文件夹。 在 `readcriticalqueue` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：

    ```cmd/sh
    npm init
    ```

2. 在 `readcriticalqueue` 文件夹中的命令提示符下，运行以下命令安装 **azure** 包：

    ```cmd/sh
    npm install azure --save
    ```

3. 使用文本编辑器在 `readcriticalqueue` 文件夹中创建 **ReadCriticalQueue.js** 文件。

4. 在 **ReadCriticalQueue.js** 文件的开头添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. 添加以下变量声明，并将占位符值替换为 IoT 服务总线连接字符串和队列名称：

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. 添加以下两个函数用于在控制台中列显输出：

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. 保存并关闭 **ReadCriticalQueue.js** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在即可运行 3 个应用程序。

1. 若要运行 **ReadDeviceToCloudMessages.js** 应用程序，请在命令提示符或 shell 中导航到 readdevicetocloudmessages 文件夹并执行以下命令：

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![运行 read-d2c-messages][readd2c]

2. 若要运行 **ReadCriticalQueue.js** 应用程序，请在命令提示符或 shell 中导航到 readcriticalqueue 文件夹并执行以下命令：

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![运行 read-critical-messages][readqueue]

3. 若要运行 **SimulatedDevice.js** 应用，请在命令提示符或外壳处导航到 simulateddevice 文件夹并执行以下命令：

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![运行 simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>（可选）将存储容器添加到 IoT 中心并向其路由消息

在本部分中，将创建一个存储帐户并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息上的现有属性发送消息到该帐户。 有关如何管理存储的详细信息，请参阅 [Azure 存储入门][Azure 存储]。

 > [!NOTE]
   > 如果并不局限于一个**终结点**，则除了 **CriticalQueue** 以外，还可以设置 **StorageContainer**，并同时运行两者。

1. 根据 [Azure 存储文档][lnk-storage]中所述创建存储帐户。 记下帐户名称。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

3. 在“终结点”边栏选项卡中选择“CriticalQueue”终结点，单击“删除”。 单击“是”，然后单击“添加”。 将终结点命名为 **StorageContainer**，使用下拉列表选择“Azure 存储容器”，并创建**存储帐户**和**存储容器**。  请记下名称。  完成后，单击底部的“确定”。 

 > [!NOTE]
   > 如果并不局限于一个**终结点**，则不需要删除 **CriticalQueue**。

4. 在 IoT 中心单击“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“设备消息”作为数据源。 输入 `level="storage"` 作为条件，并选择自定义终结点 **StorageContainer** 作为路由规则终结点。 在底部单击“保存”。  

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

1. 确保以前的应用程序 **SimulatedDevice.js** 仍在运行。 

1. 在 Azure 门户中，转到自己的存储帐户，在“Blob 服务”下面单击“浏览 Blob...”。选择自己的容器，导航到并单击 JSON 文件，然后单击“下载”以查看数据。

## <a name="next-steps"></a>后续步骤

在本教程中，介绍了如何使用 IoT 中心的消息路由功能可靠地分派设备到云的消息。

[如何使用 IoT 中心发送云到设备的消息][lnk-c2d]介绍了如何从解决方案后端向设备发送消息。

若要查看使用 IoT 中心的完整端到端解决方案的示例，请参阅 [Azure IoT 套件][lnk-suite]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-devguide-messaging]。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure 存储]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服务总线]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中心开发人员指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT 中心入门]: iot-hub-node-node-getstarted.md
[Azure IoT 开发人员中心]: https://azure.microsoft.com/develop/iot
[暂时性故障处理]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[暂时性故障处理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/