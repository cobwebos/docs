---
title: 快速入门：如何在 Node.js 中使用 azure/service-bus 队列
description: 快速入门：了解如何通过使用 azure/service-bus 包的 Node.js 应用在 Azure 中使用服务总线队列。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 9901ccd6bb1abf27bb1141c618d0bfde167b9cc3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721685"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>快速入门：如何通过 Node.js 和 azure/service-bus 包使用服务总线队列
> [!div class="op_multi_selector" title1="编程语言" title2="Node.js 包"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

本教程介绍如何使用新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包编写一个 Nodejs 程序，用于向/从服务总线队列发送/接收消息。 此包使用速度更快的 [AMQP 1.0 协议](service-bus-amqp-overview.md)，而旧版 [azure-sb](https://www.npmjs.com/package/azure-sb) 包使用的是[服务总线 REST 运行时 API](/rest/api/servicebus/service-bus-runtime-rest)。 示例是使用 JavaScript 编写的。

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线实例的连接字符串以及创建的队列的名称。 我们将在示例中使用这些值。

> [!NOTE]
> - 本教程将演练可以使用 [Nodejs](https://nodejs.org/) 复制和运行的示例。 有关如何创建 Node.js 应用程序的说明，请参阅有关使用 Windows PowerShell 创建 Node.js 应用程序并将其部署到 [Azure 网站](../app-service/app-service-web-get-started-nodejs.md)或 [Node.js 云服务](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)的文章。
> - 新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包目前不支持创建队列。 若要以编程方式创建队列，请使用 [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) 包。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node 包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
若要与服务总线队列交互，首先需要实例化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 类，并使用它来实例化 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 类。 安装队列客户端后，可以创建发送方，并在其上使用 [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 或 [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 方法发送消息。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 `send.js` 的文件，并在其中粘贴以下代码。 此代码向队列发送 10 条消息。

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
3. 输入以上代码中显示的连接字符串和队列名称。
4. 然后，在命令提示符中运行命令 `node send.js` 以执行此文件。

祝贺你！ 你已将消息发送到服务总线队列。

消息中包含 `label` 和 `messageId` 等标准属性，在发送时可以设置这些属性。 若要设置任何自定义属性，请使用 `userProperties`（一个可以保存自定义数据的键值对的 JSON 对象）。

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 一个队列中包含的消息数量不受限制，但消息的总大小受限制。 此队列大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额](service-bus-quotas.md)。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
若要与服务总线队列交互，首先需要实例化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 类，并使用它来实例化 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 类。 安装队列客户端后，可以创建接收方，并在其上使用 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 或 [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 方法来接收消息。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 `recieve.js` 的文件，并在其中粘贴以下代码。 此代码尝试从队列接收 10 条消息。 收到的实际消息计数取决于队列中的消息数以及网络延迟。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
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
3. 输入以上代码中显示的连接字符串和队列名称。
4. 然后，在命令提示符中运行命令 `node receiveMessages.js` 以执行此文件。

祝贺你！ 你已从服务总线队列收到了消息。

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) 方法采用 `ReceiveMode`（值为 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 和 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) 的枚举）。 如果你使用 `PeekLock` 模式，请记得对消息使用 `complete()`、`abandon()`、`defer()` 或 `deadletter()` 方法来[最终处理消息](message-transfers-locks-settlement.md#settling-receive-operations)。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
若要了解更多信息，请参阅以下资源：
- [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- 查看 [GitHub 上服务总线的其他 Nodejs 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)

