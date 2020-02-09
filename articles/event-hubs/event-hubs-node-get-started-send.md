---
title: 使用 Node.js（旧版）向/从 Azure 事件中心发送/接收事件
description: 本文提供了创建 Node.js 应用程序的演练，该应用程序使用旧的 azure/event-hubs 版本 2 包向/从 Azure 事件中心发送/接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 9aa2418657c2d3bcab9ef8883e5bd57422ce5e29
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899893"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs-azureevent-hubs-version-2"></a>快速入门：使用 Node.js（@azure/event-hubs 版本 2）向/从 Azure 事件中心发送/接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何创建用于向/从事件中心发送/接收事件的 Node.js 应用程序。

> [!WARNING]
> 本快速入门适用于 Azure 事件中心 Java Script SDK 版本 2。 我们建议你将代码[迁移](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)到 [Java Script SDK 版本 5](get-started-node-send-v2.md)。 



## <a name="prerequisites"></a>必备条件

若要完成本教程，需要具备以下先决条件：

- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。
- Visual Studio Code（推荐使用）或任何其他 IDE
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的程序进行操作，然后继续执行本教程的以下步骤。 然后，按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 本教程后面的步骤将使用此连接字符串。


### <a name="install-npm-package"></a>安装 npm 包
若要安装[事件中心的 npm 包](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令。

```shell
npm install @azure/event-hubs@2
```

若要安装[事件处理器主机的 npm 包](https://www.npmjs.com/package/@azure/event-processor-host)，请运行以下命令

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>发送事件

本部分介绍如何创建一个向事件中心发送事件的 Node.js 应用程序。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 创建名为 `send.js` 的文件，并在其中粘贴以下代码。 按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 输入以上代码中显示的连接字符串和事件中心名称
4. 然后，在命令提示符中运行命令 `node send.js` 以执行此文件。 此命令将向事件中心发送 100 个事件

祝贺你！ 现已向事件中心发送事件。


## <a name="receive-events"></a>接收事件

本部分介绍如何创建一个可从事件中心默认使用者组的单个分区接收事件的 Node.js 应用程序。 

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 创建名为 `receive.js` 的文件，并在其中粘贴以下代码。
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 输入以上代码中显示的连接字符串和事件中心名称。
4. 然后，在命令提示符中运行命令 `node receive.js` 以执行此文件。 此命令将从事件中心内默认使用者组的某个分区接收事件

祝贺你！ 现已从事件中心收到事件。

## <a name="receive-events-using-event-processor-host"></a>使用事件处理程序主机接收事件

本部分介绍如何在 Node.js 应用程序中使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 从事件中心接收事件。 EventProcessorHost (EPH) 通过在事件中心的使用者组中的所有分区中创建接收器，帮助你高效地从事件中心接收事件。 它定期在 Azure 存储 Blob 中为收到的消息的元数据创建检查点。 使用此方式，可以很容易地在以后的某个时间从离开的位置继续接收消息。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 创建名为 `receiveAll.js` 的文件，并在其中粘贴以下代码。
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. 输入以上代码中显示的连接字符串和事件中心名称，并输入 Azure Blob 存储的连接字符串
4. 然后，在命令提示符中运行命令 `node receiveAll.js` 以执行此文件。

祝贺你！ 现已使用事件处理程序主机从事件中心收到事件。 此命令将从事件中心内默认使用者组的所有分区接收事件

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中心的功能和术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)
- 在 GitHub 上查看[事件中心](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples)和[事件处理器主机](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples)的其他 Node.js 示例
