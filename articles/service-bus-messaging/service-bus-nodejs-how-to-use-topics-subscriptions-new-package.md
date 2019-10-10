---
title: 如何通过 Node.js 使用 Azure 服务总线主题和订阅 | Microsoft Docs
description: 了解如何在来自 Node.js 应用的 Azure 中使用服务总线主题和订阅。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f927274e1e866a9cba72330280316cc5ee7d8047
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178061"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>如何将服务总线主题和订阅与 Node.js 和 azure/service-bus 包配合使用
> [!div class="op_multi_selector" title1="编程语言" title2="Node.js 包"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

本教程介绍如何使用新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包编写一个 Node.js 程序，用于将消息发送到服务总线主题，并从服务总线订阅接收消息。 此包使用速度更快的 [AMQP 1.0 协议](service-bus-amqp-overview.md)，而旧版 [azure-sb](https://www.npmjs.com/package/azure-sb) 包使用的是[服务总线 REST 运行时 API](/rest/api/servicebus/service-bus-runtime-rest)。 示例是使用 JavaScript 编写的。

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果你没有可用的主题和订阅，请遵循[使用 Azure 门户创建服务总线主题和订阅](service-bus-quickstart-topics-subscriptions-portal.md)一文中的步骤创建主题和订阅。 记下服务总线实例的连接字符串，以及创建的主题和订阅的名称。 我们将在示例中使用这些值。

> [!NOTE]
> - 本教程将演练可以使用 [Nodejs](https://nodejs.org/) 复制和运行的示例。 有关如何创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站](../app-service/app-service-web-get-started-nodejs.md)或[使用 Windows PowerShell 创建 Node.js 云服务](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。
> - 新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包目前不支持创建主题和订阅。 若要以编程方式创建队列，请使用 [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) 包。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node 包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
若要与服务总线主题交互，首先需要实例化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 类，并使用它来实例化 [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) 类。 安装主题客户端后，可以创建发送方，并在其上使用 [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 或 [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 方法发送消息。

1. 打开你喜好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `send.js` 的文件，并将下面的代码粘贴到其中。 此代码会将 10 条消息发送到主题。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 输入以上代码中显示的连接字符串和主题名称。
4. 然后在命令提示符下运行命令 `node send.js` 以执行此文件。 

祝贺你！ 你已将消息发送到服务总线队列。

消息中包含 `label` 和 `messageId` 等标准属性，在发送时可以设置这些属性。 若要设置任何自定义属性，请使用 `userProperties`（一个可以保存自定义数据的键值对的 JSON 对象）。

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 一个主题中包含的消息数量不受限制，但消息的总大小受限制。 此主题大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额](service-bus-quotas.md)。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
若要与服务总线订阅交互，首先需要实例化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 类，并使用它来实例化 [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) 类。 安装订阅客户端后，可以创建接收方，并在其上使用 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 或 [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 方法来接收消息。

1. 打开你喜好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `recieve.js` 的文件，并将下面的代码粘贴到其中。 此代码尝试从订阅接收 10 条消息。 收到的实际消息计数取决于订阅中的消息数以及网络延迟。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 输入以上代码中显示的连接字符串以及主题和订阅的名称。
4. 然后在命令提示符下运行命令 `node receiveMessages.js` 以执行此文件。

祝贺你！ 你已从服务总线订阅收到了消息。

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) 方法采用 `ReceiveMode`（值为 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 和 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) 的枚举）。 如果你使用 `PeekLock` 模式，请记得对消息使用 `complete()`、`abandon()`、`defer()` 或 `deadletter()` 来[最终处理消息](message-transfers-locks-settlement.md#settling-receive-operations)。

## <a name="subscription-filters-and-actions"></a>订阅筛选器和操作
服务总线支持[对订阅使用筛选器和操作](topic-filters.md)，这样，你就可以根据订阅筛选传入的消息并编辑其属性。

获取 `SubscriptionClient` 的实例后，可对其使用以下方法，以在订阅中获取、添加和删除用于控制筛选器与操作的规则。

- getRules
- addRule
- removeRule

在每个订阅中，有一个默认规则使用 true 筛选器来允许所有传入消息。 添加新规则时，请记得删除默认筛选器，使新规则中的筛选器正常工作。 如果订阅中没有规则，则它不会收到任何消息。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源。

- [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- 查看 [GitHub 上服务总线的其他 Nodejs 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)


