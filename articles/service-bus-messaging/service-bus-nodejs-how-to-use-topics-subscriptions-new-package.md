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
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65992131"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>如何使用 Node.js 和 azure/服务总线包中使用服务总线主题和订阅
> [!div class="op_multi_selector" title1="编程语言" title2="Node.js 包"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

在本教程中，学习如何编写一个用于将消息发送到服务总线主题和从使用新的服务总线订阅接收消息的 Node.js 程序[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)包。 此包使用更快[AMQP 1.0 协议](service-bus-amqp-overview.md)而较旧[azure sb](https://www.npmjs.com/package/azure-sb)使用包[服务总线 REST 运行时 Api](/rest/api/servicebus/service-bus-runtime-rest)。 这些示例用 JavaScript 编写。

## <a name="prerequisites"></a>必备组件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您没有主题和订阅以使用，按照步骤[使用 Azure 门户创建服务总线主题和订阅](service-bus-quickstart-topics-subscriptions-portal.md)文章来创建它们。 请记下你的服务总线实例和创建的订阅的主题的名称的连接字符串。 在示例中，我们将使用这些值。

> [!NOTE]
> - 本教程适用于示例复制并使用运行[Nodejs](https://nodejs.org/)。 有关如何创建 Node.js 应用程序的说明，请参阅[创建和部署 Node.js 应用程序到 Azure 网站](../app-service/app-service-web-get-started-nodejs.md)，或[Node.js 云服务使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。
> - 新[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)包不支持尚未创建 topcis 和订阅。 请使用[ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus)包如果你想要以编程方式创建它们。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node 包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开命令提示符具有`npm`在其路径中，将目录更改为你想要具有您的示例，然后运行此命令的文件夹。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
交互使用服务总线主题首先实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，用来实例化[TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient)类。 主题客户端之后，可以创建一个发送方和使用两种[发送](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)上其发送消息的方法。

1. 打开你喜爱的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `send.js` 的文件，并将下面的代码粘贴到其中。 此代码将向主题发送 10 条消息。

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
3. 在上述代码中输入的连接字符串和主题的名称。
4. 然后在命令提示符下运行命令 `node send.js` 以执行此文件。 

祝贺你！ 你刚刚向服务总线队列发送消息。

消息具有一些标准属性，如`label`和`messageId`发送时可以设置的。 如果你想要设置任何自定义属性，使用`userProperties`，即可以容纳自定义数据的键 / 值对的 json 对象。

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 上一个主题中包含的消息数没有限制，但持有的主题的消息的总大小没有限制。 此主题大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额](service-bus-quotas.md)。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
交互使用服务总线订阅启动的实例化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)类，用来实例化[SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient)类。 订阅客户端之后，可以创建接收器并使用任一[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)它以接收消息的方法。

1. 打开你喜爱的编辑器，如[Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `recieve.js` 的文件，并将下面的代码粘贴到其中。 此代码将尝试从订阅接收 10 条消息。 您收到与实际计数取决于中的订阅和网络延迟的消息数。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. 在上述代码中输入的连接字符串和主题和订阅的名称。
4. 然后在命令提示符下运行命令 `node receiveMessages.js` 以执行此文件。

祝贺你！ 您只需从服务总线订阅接收消息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-)方法采用`ReceiveMode`这是一个枚举值[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)并[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)。 请记住[结清消息](message-transfers-locks-settlement.md#settling-receive-operations)如果你使用`PeekLock`模式下使用的任何`complete()`， `abandon()`， `defer()`，或`deadletter()`上消息的方法。

## <a name="subscription-filters-and-actions"></a>订阅筛选器和操作
服务总线支持[筛选器和在订阅上的操作](topic-filters.md)，这样您可以筛选发送到订阅的传入消息并编辑其属性。

实例后`SubscriptionClient`可以使用以下方法以获取，添加和删除要控制筛选器和操作的订阅上的规则。

- getRules
- addRule
- removeRule

每个订阅具有使用真正的筛选器以允许所有传入消息的默认规则。 在添加新规则，请记得删除默认筛选器使你的新规则中的筛选器，若要运行。 如果订阅没有任何规则，它将接收任何消息。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅以下资源。

- [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- 签出其他[GitHub 上的服务总线的 Nodejs 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)


