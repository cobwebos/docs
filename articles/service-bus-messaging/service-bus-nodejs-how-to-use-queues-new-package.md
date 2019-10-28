---
title: 如何在 node.js 中使用 azure/服务总线队列
description: 了解如何使用 azure/服务总线包通过 node.js 应用在 Azure 中使用服务总线队列。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790307"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>如何将 Service Bus 队列与 node.js 和 azure/服务总线包配合使用
> [!div class="op_multi_selector" title1="编程语言" title2="Node.js 程序包"]
> - [（Node.js | azure-sb）](service-bus-nodejs-how-to-use-queues.md)
> - [（Node.js | @azure/service-bus）](service-bus-nodejs-how-to-use-queues-new-package.md)

在本教程中，将了解如何编写 Nodejs 程序，以使用新的[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)包将消息发送到服务总线队列，以及从服务总线队列接收消息。 此包使用更快的[AMQP 1.0 协议](service-bus-amqp-overview.md)，而使用较旧的[azure sb](https://www.npmjs.com/package/azure-sb)包来使用[服务总线 REST 运行时 api](/rest/api/servicebus/service-bus-runtime-rest)。 示例是用 JavaScript 编写的。

## <a name="prerequisites"></a>必备组件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活[MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有要使用的队列，请按照[使用 Azure 门户创建服务总线队列一](service-bus-quickstart-portal.md)文中的步骤来创建队列。 记下你的服务总线实例的连接字符串和你创建的队列的名称。 在示例中，我们将使用这些值。

> [!NOTE]
> - 本教程适用于使用[Nodejs](https://nodejs.org/)可以复制和运行的示例。 有关如何创建 node.js 应用程序的说明，请参阅[使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)[创建 node.js 应用程序并将其部署到 Azure 网站](../app-service/app-service-web-get-started-nodejs.md)或 node.js 云服务。
> - 新的[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)包尚不支持创建队列。 如果要以编程方式创建包，请使用[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)包。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node 包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开在其路径中 `npm` 的命令提示符，将目录更改为要在其中安装示例的文件夹，然后运行此命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
与 Service Bus 队列交互后，开始使用实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，并使用它来实例化[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)类。 拥有队列客户端后，可以创建发送方，并在其上使用[send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)方法发送消息。

1. 打开你最喜欢的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `send.js` 的文件，并将下面的代码粘贴到其中。 此代码会将10条消息发送到队列。

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
3. 在上面的代码中输入连接字符串和队列名称。
4. 然后，在命令提示符下运行命令 `node send.js` 以执行此文件。

祝贺你！ 你刚刚将消息发送到服务总线队列。

消息具有一些可以在发送时设置 `label` 和 `messageId` 等标准属性。 如果要设置任何自定义属性，请使用 `userProperties`，这是一个可保存自定义数据的键值对的 json 对象。

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 队列中包含的消息数量没有限制，但队列持有的消息的总大小有上限。 此队列大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额](service-bus-quotas.md)。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
与 Service Bus 队列交互后，开始使用实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，并使用它来实例化[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)类。 拥有队列客户端后，可以创建接收方，并使用[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)方法来接收消息。

1. 打开你最喜欢的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `recieve.js` 的文件，并将下面的代码粘贴到其中。 此代码将尝试从队列接收10条消息。 收到的实际计数取决于队列中的消息数和网络延迟。

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
3. 在上面的代码中输入连接字符串和队列名称。
4. 然后，在命令提示符下运行命令 `node receiveMessages.js` 以执行此文件。

祝贺你！ 刚接收到来自服务总线队列的消息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-)方法采用 `ReceiveMode`，它是值为[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)和[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)的枚举。 如果对消息使用 `complete()`、`abandon()`、`defer()`或 `deadletter()` 方法中的任意一种，请记住要使用 `PeekLock` 模式来[结算消息](message-transfers-locks-settlement.md#settling-receive-operations)。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅以下资源。
- [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- [查看 GitHub 上的服务总线的其他 Nodejs 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)

