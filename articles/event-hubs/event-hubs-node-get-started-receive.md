---
title: 使用 Node.js 从 Azure 事件中心接收事件 | Microsoft Docs
description: 了解如何使用 Node.js 从事件中心接收事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 2f59b7f47acc8ce8e566b7f0e2bcfed379ea60d2
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239078"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>使用 Node.js 从 Azure 事件中心接收事件

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。 还可以在处理事件数据之前在 Azure 存储或 Azure Data Lake Store 中捕获该数据。  

若要了解有关事件中心的详细信息，请参阅[事件中心概述](event-hubs-about.md)。

本教程展示了如何在 Node.js 应用程序中使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 从事件中心接收事件。 EventProcessorHost (EPH) 通过在事件中心的使用者组中的所有分区中创建接收器，帮助你高效地从事件中心接收事件。 它定期在 Azure 存储 Blob 中为收到的消息的元数据创建检查点。 使用此方式，可以很容易地在以后的某个时间从离开的位置继续接收消息。

[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) 上提供了本快速入门的代码。

> [!NOTE]
>  若要使用 Node.js 将事件发送到事件中心，请参阅以下文章：[使用 Node.js 将事件发送到 Azure 事件中心](event-hubs-node-get-started-send.md)。 

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。不要使用 node.js 的旧版 LTS。 
- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。

## <a name="create-a-namespace-and-event-hub"></a>创建命名空间和事件中心
第一步是使用 Azure 门户创建包含事件中心的事件中心命名空间。 如果没有现成可用的，可以按照[使用 Azure 门户创建事件中心命名空间和事件中心](event-hubs-create.md)中的说明创建这些实体。

## <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器
若要使用 EventProcessorHost，必须有一个 Azure 存储帐户。 使用 Azure 存储容器在接收器之间共享分区租用和事件流中的检查点等状态信息。 可以按照[此文章](../storage/common/storage-quickstart-create-account.md)中的说明创建 Azure 存储帐户。

## <a name="clone-the-git-repository"></a>克隆 Git 存储库
从 Github 下载或克隆[示例](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)。 

## <a name="install-the-eventprocessorhost"></a>安装 EventProcessorHost
为事件中心模块安装 EventProcessorHost。 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>使用 EventProcessorHost 接收事件
你克隆的 SDK 包含了多个示例，其中展示了如何使用 Node.js 从事件中心接收事件。 在本快速入门中，你将使用 **singleEPH.js** 示例。 若要观察正在接收的事件，请打开另一个终端，并使用[发送示例](event-hubs-node-get-started-send.md)来发送事件。

1. 在 Visual Studio Code 中打开项目。 
2. 在 **processor** 文件夹下创建一个名为 **.env** 的文件。 从根文件夹中的 **sample.env** 中复制并粘贴示例环境变量。
3. 配置你的事件中心连接字符串、事件中心名称和存储终结点。 可以在 Azure 门户中从“事件中心”页面上的 **RootManageSharedAccessKey** 下的“连接字符串-主要”密钥下复制你的事件中心的连接字符串。 有关详细步骤，请参阅[获取连接字符串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 中，导航到 **processor** 文件夹路径。 通过运行以下命令安装节点程序包并生成项目：

    ```nodejs
    npm i
    npm run build
    ```
5. 通过运行以下命令使用事件处理器主机接收事件：

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>查看示例代码 
下面是使用 node.js 从事件中心接收事件的示例代码。 可以手动创建 sampleEph.js 文件，并运行它来从事件中心接收事件。 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

在运行此脚本之前，请记得设置环境变量。 可以在命令行中如以下示例所示配置此变量，也可以使用 [dotenv 程序包](https://www.npmjs.com/package/dotenv#dotenv)。 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

可以在[此处](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)找到更多示例。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建从事件中心接收消息的 Node.js 应用程序。 若要了解如何使用 Node.js 将事件发送到事件中心，请参阅[从事件中心发送事件 - Node.js](event-hubs-node-get-started-send.md)。

<!-- Links -->
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
