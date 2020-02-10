---
title: 使用 Node.js 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure/event-hubs 版本 5 包向/从 Azure 事件中心发送/接收事件的 Node.js 应用程序。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906369"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>使用 Node.js（azure/event-hubs 版本 5）向/从事件中心发送/接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到事件中心的数据。 有关详细信息，请参阅[事件中心概述](event-hubs-about.md)和[事件中心的功能](event-hubs-features.md)。

本快速入门介绍如何创建可向/从事件中心发送/接收事件的 Node.js 应用程序。

> [!IMPORTANT]
> 本快速入门使用 Azure 事件中心 JavaScript SDK 版本 5。 有关使用 JavaScript SDK 版本 2 的快速入门，请参阅[此文](event-hubs-node-get-started-send.md)。 

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。  
- Node.js 版本 8.x 或更高版本。 下载最新的[长期支持 (LTS) 版本](https://nodejs.org)。  
- Visual Studio Code（推荐）或任何其他集成开发环境 (IDE)。  
- 有效的事件中心命名空间和事件中心。 若要创建它们，请执行以下步骤： 

   1. 在 [Azure 门户](https://portal.azure.com)中，创建“事件中心”类型的命名空间，然后获取应用程序与事件中心进行通信所需的管理凭据。  
   1. 若要创建命名空间和事件中心，请按照以下文章中的说明操作：[快速入门：使用 Azure 门户创建事件中心](event-hubs-create.md)。
   1. 按照本快速入门中的说明继续操作。 
   1. 若要获取事件中心命名空间的连接字符串，请按照[获取连接字符串 ](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的说明操作。 请记下该连接字符串，以便稍后在本快速入门中使用。

### <a name="install-the-npm-package"></a>安装 npm 包
若要安装[适用于事件中心的 Node 包管理器 (npm) 包](https://www.npmjs.com/package/@azure/event-hubs)，请打开路径中包含 *npm* 的命令提示符，将目录切换到用于保存示例的文件夹，然后运行以下命令：

```shell
npm install @azure/event-hubs
```

对于接收端，需要额外安装两个包。 本快速入门将使用 Azure Blob 存储来保存检查点，使程序不会读取已读取的事件。 它在 Blob 中按固定的时间间隔对收到的消息执行元数据检查点。 使用此方式可以很容易地在以后的某个时间从退出的位置继续接收消息。

运行以下命令：

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>发送事件

在本部分，你将创建一个向事件中心发送事件的 Node.js 应用程序。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 创建名为 *send.js* 的文件，然后将以下代码粘贴到其中：

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. 在代码中，使用实际值替换以下内容：
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. 运行 `node send.js` 以执行此文件。 此命令将包含三个事件的批发送到事件中心。
1. 在 Azure 门户中，验证事件中心是否已收到消息。 在“指标”部分切换到“消息”视图。   刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。

    [![验证事件中心是否已收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 有关完整源代码（包括附加的参考注释），请参阅 [GitHub sendEvents.js 页](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)。

祝贺你！ 现已向事件中心发送事件。


## <a name="receive-events"></a>接收事件
在本部分，你将在 Node.js 应用程序中使用 Azure Blob 存储检查点存储从事件中心接收事件。 该应用程序将在 Azure 存储 Blob 中定期针对收到的消息执行元数据检查点。 使用此方式可以很容易地在以后的某个时间从退出的位置继续接收消息。

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>创建 Azure 存储帐户和 Blob 容器
若要创建 Azure 存储帐户并在其中创建 Blob 容器，请执行以下操作：

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [在存储帐户中创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

请务必记下连接字符串和容器名称，供稍后在接收代码中使用。

### <a name="write-code-to-receive-events"></a>编写用于接收事件的代码

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 创建名为 *receive.js* 的文件，然后将以下代码粘贴到其中：

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. 在代码中，使用实际值替换以下值：
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. 在命令提示符下运行 `node receive.js` 以执行此文件。 窗口中应会显示有关已收到事件的消息。

    > [!NOTE]
    > 有关完整源代码（包括附加的参考注释），请参阅 [GitHub receiveEventsUsingCheckpointStore.js 页](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)。

祝贺你！ 现已从事件中心收到事件。 接收器程序将从事件中心内默认使用者组的所有分区接收事件。

## <a name="next-steps"></a>后续步骤
查看 GitHub 中的以下示例：

- [JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
