---
title: 如何使用 Node.js 的 azure/服务总线中的 Azure 服务总线队列 |Microsoft Docs
description: 了解如何在来自 Node.js 应用程序的 Azure 中使用服务总线队列。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988353"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>如何使用 Node.js 和 azure/服务总线包使用服务总线队列
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

在本教程中，学习如何编写一个 Nodejs 程序来将消息发送到和从使用新的服务总线队列接收消息[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)包。 此包使用更快[AMQP 1.0 协议](service-bus-amqp-overview.md)而较旧[azure sb](https://www.npmjs.com/package/azure-sb)使用包[服务总线 REST 运行时 Api](/rest/api/servicebus/service-bus-runtime-rest)。 这些示例用 JavaScript 编写。

## <a name="prerequisites"></a>必备组件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 请记下你的服务总线实例和你创建的队列的名称的连接字符串。 在示例中，我们将使用这些值。

> [!NOTE]
> - 本教程适用于示例复制并使用运行[Nodejs](https://nodejs.org/)。 有关如何创建 Node.js 应用程序的说明，请参阅[创建和部署 Node.js 应用程序到 Azure 网站](../app-service/app-service-web-get-started-nodejs.md)，或[Node.js 云服务使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。
> - 新[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)包不支持尚未创建队列。 请使用[ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus)包如果你想要以编程方式创建它们。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node 包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开命令提示符具有`npm`在其路径中，将目录更改为你想要具有您的示例，然后运行此命令的文件夹。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
交互使用服务总线队列将启动与实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，用来实例化[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)类。 队列客户端之后，可以创建一个发送方和使用两种[发送](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)上其发送消息的方法。

1. 打开你喜爱的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为文件`send.js`并粘贴以下代码到它。 此代码将向您的队列发送 10 条消息。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述代码中输入的连接字符串和队列的名称。
4. 然后运行命令`node send.js`在命令提示符中执行此文件。

恭喜! 你刚刚向服务总线队列发送消息。

消息具有一些标准属性，如`label`和`messageId`发送时可以设置的。 如果你想要设置任何自定义属性，使用`userProperties`，即可以容纳自定义数据的键 / 值对的 json 对象。

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 在队列中包含的消息数量没有限制，但有持有的队列的消息的总大小是上限。 此队列大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额](service-bus-quotas.md)。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
交互使用服务总线队列将启动与实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，用来实例化[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)类。 队列客户端之后，可以创建接收器并使用任一[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)它以接收消息的方法。

1. 打开你喜爱的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为文件`recieve.js`并粘贴以下代码到它。 此代码将尝试从队列接收 10 条消息。 您收到与实际计数取决中队列和网络延迟的消息数。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述代码中输入的连接字符串和队列的名称。
4. 然后运行命令`node receiveMessages.js`在命令提示符中执行此文件。

恭喜! 您只需从服务总线队列接收消息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-)方法采用`ReceiveMode`这是一个枚举值[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)并[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)。 请记住[结清消息](message-transfers-locks-settlement.md#settling-receive-operations)如果你使用`PeekLock`模式下使用的任何`complete()`， `abandon()`， `defer()`，或`deadletter()`上消息的方法。

> [!NOTE]
> 你可以管理与服务总线资源[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)。 服务总线资源管理器允许用户连接到服务总线命名空间并轻松管理消息实体。 该工具提供高级的功能，如导入/导出功能或测试主题、 队列、 订阅、 中继服务、 通知中心和事件中心的功能。 

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅以下资源。
- [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- 签出其他[GitHub 上的服务总线的 Nodejs 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)

