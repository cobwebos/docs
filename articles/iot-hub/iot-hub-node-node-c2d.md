---
title: 使用 Azure IoT 中心发送云到设备消息 (Node) | Microsoft Docs
description: 如何使用 Azure IoT SDK for Node.js 将云到设备的消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110801"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>通过 IoT 中心发送云到设备的消息（node.js）

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 将[遥测数据从设备发送到 iot 中心](quickstart-send-telemetry-node.md)快速入门演示如何创建 iot 中心、在其中预配设备标识，以及编写用于发送设备到云消息的模拟设备应用程序。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程的基础[是将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)。 其中了说明了如何：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。
* 在设备上接收云到设备的消息。
* 从解决方案后端，请求传递从 IoT 中心发送到设备的消息的确认（*反馈*）。

可以在[IoT 中心开发人员指南](iot-hub-devguide-messaging.md)中找到有关云到设备消息的详细信息。

在本教程结束时，会运行两个 Node.js 控制台应用：

* **SimulatedDevice**，在[将遥测从设备发送到 iot 中心时](quickstart-send-telemetry-node.md)创建的应用的修改版本，它连接到 iot 中心并接收云到设备的消息。

* **SendCloudToDeviceMessage**，它通过 IoT 中心将云到设备的消息发送到模拟设备应用，然后接收其传递确认。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 Sdk 对许多设备平台和语言（包括 C、Java、Python 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的逐步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。
>

## <a name="prerequisites"></a>先决条件

* Node.js 版本 10.0. x 或更高版本。 [准备开发环境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)介绍如何在 Windows 或 Linux 上安装本教程所用的 node.js。

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial)。）

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用中接收消息

在本部分中，将修改在[将遥测从设备发送到 iot 中心](quickstart-send-telemetry-node.md)中创建的模拟设备应用，以接收来自 iot 中心的云到设备消息。

1. 使用文本编辑器打开**SimulatedDevice**文件。 此文件位于[从设备发送遥测到 iot 中心](quickstart-send-telemetry-node.md)快速入门中下载的 node.js 示例代码的根文件夹中的**iot-hub\Quickstarts\simulated-device**文件夹中。

2. 向设备客户端注册处理程序，以接收从 IoT 中心发送的消息。 将调用添加到在创建设备客户端的行之后 `client.on`，如以下代码片段所示：

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    在此示例中，设备将调用**complete**函数以通知 IoT 中心它已处理了该消息。 如果你使用的是 MQTT 传输，则不需要**完成**对的调用。 这是 HTTPS 和 AMQP 所必需的。
  
   > [!NOTE]
   > 如果使用 HTTPS（而不使用 MQTT 或 AMQP）作为传输，则 DeviceClient 实例将不会频繁（频率低于每 25 分钟一次）检查 IoT 中心发来的消息。 有关 MQTT、AMQP 和 HTTPS 支持之间的差异以及 IoT 中心限制的详细信息，请参阅[Iot 中心开发人员指南](iot-hub-devguide-messaging.md)。
   >

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

本文介绍如何创建一个后端服务，通过[从设备向 IoT 中心发送遥测](quickstart-send-telemetry-node.md)数据创建的 IoT 中心发送云到设备的消息。 若要发送云到设备的消息，服务需要**服务连接**权限。 默认情况下，每个 IoT 中心都创建有一个名为 "**服务**" 的共享访问策略，该策略将授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

在本部分中，创建一个 Node.js 控制台应用程序，它将云到设备的消息发送到模拟设备应用程序。 需要在将[遥测从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)快速入门中添加的设备的设备 ID。 你还需要之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。

1. 创建名为 **sendcloudtodevicemessage** 的空文件夹。 在命令提示符处，使用以下命令在 **sendcloudtodevicemessage** 文件夹中创建一个 package.json 文件。 接受所有默认值：

    ```shell
    npm init
    ```

2. 在命令提示符处，运行以下命令在 **sendcloudtodevicemessage** 文件夹中安装 **azure iothub** 包：

    ```shell
    npm install azure-iothub --save
    ```

3. 通过文本编辑器，在 **sendcloudtodevicemessage**文件夹中创建一个 **SendCloudToDeviceMessage.js** 文件。

4. 在 `require`SendCloudToDeviceMessage.js**文件的开头添加以下** 语句：

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. 将以下代码添加到 **SendCloudToDeviceMessage.js** 文件。 用之前记下的 IoT 中心连接字符串和设备 ID 替换 "{iot 中心连接字符串}" 和 "{device id}" 占位符值：

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 添加以下函数，以便在控制台中列显操作结果：

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 添加以下函数，以便在控制台中列显送达反馈消息：

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 添加以下代码，以便在设备确认收到云到设备的消息时会消息发送到设备，并处理反馈消息：

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. 保存并关闭 **SendCloudToDeviceMessage.js** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在**模拟设备**文件夹的命令提示符下，运行以下命令将遥测发送到 IoT 中心，并侦听云到设备的消息：

    ```shell
    node SimulatedDevice.js
    ```

    ![运行模拟设备应用](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 在 **sendcloudtodevicemessage** 文件夹中的命令提示符下，运行以下命令发送云到设备的消息并等待确认反馈：

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![运行应用以发送云到设备的命令](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
   >

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何发送和接收云到设备的消息。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
