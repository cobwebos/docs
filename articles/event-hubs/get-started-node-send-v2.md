---
title: 使用 Node.js 发送和接收事件 - Azure 事件中心
description: 本文提供了一个演练，说明如何创建从 Azure 事件中心发送事件的 Node.js 应用程序。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981616"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>使用 Node.js 向/从 Azure 事件中心发送/接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何创建用于向/从事件中心发送/接收事件的 Node.js 应用程序。

> [!IMPORTANT]
> 此快速入门使用 Azure 事件中心 Java 脚本 SDK 版本5。 有关使用 Java 脚本 SDK 旧版本2的快速入门，请参阅[此文](event-hubs-node-get-started-send.md)。 如果使用的是 SDK 版本2，则建议将代码迁移到最新版本。 有关详细信息，请参阅[迁移指南](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)。

## <a name="prerequisites"></a>必备组件

若要完成本教程，需要具备以下先决条件：

- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。
- Visual Studio Code（推荐使用）或任何其他 IDE
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的程序进行操作，然后继续执行本教程的以下步骤。 然后，按照 "[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)" 一文中的说明，获取事件中心命名空间的连接字符串。 本教程后面的步骤将使用此连接字符串。


### <a name="install-npm-packages"></a>安装 npm 包
若要安装[事件中心的 npm 包](https://www.npmjs.com/package/@azure/event-hubs)，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令。

```shell
npm install @azure/event-hubs
```

对于接收方，需另外安装两个包。 在本快速入门中，你将使用 Azure Blob 存储来保存检查点，以便程序不会读取它已读取的事件。 它在 blob 中定期检查收到的消息中的元数据。 使用此方式，可以很容易地在以后的某个时间从离开的位置继续接收消息。

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>发送事件

本部分介绍如何创建一个向事件中心发送事件的 Node.js 应用程序。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
2. 创建名为 `send.js` 的文件，并将以下代码粘贴到其中。

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 不要忘记替换代码中的**连接字符串**和**事件中心的名称**值。
5. 运行命令 `node send.js` 以执行此文件。 这会将三个事件的一批发送到事件中心
6. 在 Azure 门户中，可以验证事件中心是否已收到消息。 切换到 "**指标**" 部分中的 "**消息**" 视图。 刷新页面以更新图表。 它可能需要几秒钟时间才能显示收到的消息。

    [![验证事件中心是否收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 有关完整的源代码和详细信息注释，请参阅[GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

祝贺你！ 现已向事件中心发送事件。


## <a name="receive-events"></a>接收事件
本部分演示如何使用 node.js 应用程序中的 Azure Blob 检查点存储从事件中心接收事件。 它定期在 Azure 存储 Blob 中为收到的消息的元数据创建检查点。 使用此方式，可以很容易地在以后的某个时间从离开的位置继续接收消息。

### <a name="create-an-azure-storage-and-a-blob-container"></a>创建 Azure 存储和 blob 容器
按照以下步骤创建 Azure 存储帐户中的 blob 容器。

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    记下连接字符串和容器名称。 您将在接收代码中使用它们。

### <a name="write-code-to-receive-events"></a>编写代码来接收事件

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
2. 创建名为 `receive.js` 的文件，并将以下代码粘贴到其中。 有关详细信息，请参阅代码注释。
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
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
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
3. 请不要忘记在代码中指定**以下值**：
    - 事件中心命名空间的连接字符串
    - 事件中心的名称
    - Azure 存储帐户的连接字符串
    - Blob 容器的名称
5. 然后，在命令提示符中运行命令 `node receive.js` 以执行此文件。 应会在窗口中看到关于接收的事件的消息。

    > [!NOTE]
    > 有关完整的源代码和详细信息注释，请参阅[GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)。

祝贺你！ 现已从事件中心收到事件。 接收方程序将从事件中心内默认使用者组的所有分区接收事件

## <a name="next-steps"></a>后续步骤
查看 GitHub 上的以下示例：

- [JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
