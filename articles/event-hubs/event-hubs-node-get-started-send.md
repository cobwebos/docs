---
title: 发送和使用 Node.js 的 Azure 事件中心接收事件 |Microsoft Docs
description: 本文提供了一个演练，说明如何创建从 Azure 事件中心发送事件的 Node.js 应用程序。
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677895"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>事件发送到或从使用 Node.js 的 Azure 事件中心接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何创建 Node.js 应用程序发送到事件或从事件中心接收事件。

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>必备组件

若要完成本教程，需要具备以下先决条件：

- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。
- Visual Studio Code（推荐使用）或任何其他 IDE
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的程序进行操作，然后继续执行本教程的以下步骤。 然后，按照文章中的说明获取有关事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 本教程后面的步骤将使用此连接字符串。
- 克隆[示例 GitHub 存储库](https://github.com/Azure/azure-event-hubs-node)在计算机上。 


## <a name="send-events"></a>发送事件
本部分演示如何创建 Node.js 应用程序将事件发送到事件中心。 

### <a name="install-nodejs-package"></a>安装 Node.js 程序包
在计算机上安装适用于 Azure 事件中心的 Node.js 程序包。 

```shell
npm install @azure/event-hubs
```

如果你尚未在先决条件中所述克隆 Git 存储库，下载[示例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)从 GitHub。 

你克隆的 SDK 包含了多个示例，其中展示了如何使用 node.js 将事件发送到事件中心。 在本快速入门中，你将使用 **simpleSender.js** 示例。 若要观察正在接收的事件，请打开另一个终端，并使用[接收示例](event-hubs-node-get-started-receive.md)来接收事件。

1. 在 Visual Studio Code 中打开项目。 
2. 在 **client** 文件夹下创建一个名为 **.env** 的文件。 从根文件夹中的 **sample.env** 中复制并粘贴示例环境变量。
3. 配置你的事件中心连接字符串、事件中心名称和存储终结点。 有关获取事件中心连接字符串的说明，请参阅[获取连接字符串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 中，导航到 **client** 文件夹路径。 通过运行以下命令安装节点程序包并生成项目：

    ```shell
    npm i
    npm run build
    ```
5. 通过运行以下命令来启动发送事件： 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>查看示例代码 
查看 simpleSender.js 文件中用于将事件发送到事件中心的示例代码。

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

在运行此脚本之前，请记得设置环境变量。 可以在命令行中如以下示例所示配置这些变量，也可以使用 [dotenv 包](https://www.npmjs.com/package/dotenv#dotenv)。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>接收事件
本教程展示了如何在 Node.js 应用程序中使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 从事件中心接收事件。 EventProcessorHost (EPH) 通过在事件中心的使用者组中的所有分区中创建接收器，帮助你高效地从事件中心接收事件。 它定期在 Azure 存储 Blob 中为收到的消息的元数据创建检查点。 使用此方式，可以很容易地在以后的某个时间从离开的位置继续接收消息。

[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) 上提供了本快速入门的代码。

### <a name="clone-the-git-repository"></a>克隆 Git 存储库
从 GitHub 下载或克隆[示例](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)。 

### <a name="install-the-eventprocessorhost"></a>安装 EventProcessorHost
为事件中心模块安装 EventProcessorHost。 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>使用 EventProcessorHost 接收事件
你克隆的 SDK 包含了多个示例，其中展示了如何使用 Node.js 从事件中心接收事件。 在本快速入门中，你将使用 **singleEPH.js** 示例。 若要观察正在接收的事件，请打开另一个终端，并使用[发送示例](event-hubs-node-get-started-send.md)来发送事件。

1. 在 Visual Studio Code 中打开项目。 
2. 在 **processor** 文件夹下创建一个名为 **.env** 的文件。 从根文件夹中的 **sample.env** 中复制并粘贴示例环境变量。
3. 配置你的事件中心连接字符串、事件中心名称和存储终结点。 可以在 Azure 门户中从“事件中心”页面上的 **RootManageSharedAccessKey** 下的“连接字符串-主要”密钥下复制你的事件中心的连接字符串。 有关详细步骤，请参阅[获取连接字符串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 中，导航到 **processor** 文件夹路径。 通过运行以下命令安装节点程序包并生成项目：

    ```shell
    npm i
    npm run build
    ```
5. 通过运行以下命令使用事件处理器主机接收事件：

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>查看示例代码 
下面是使用 node.js 从事件中心接收事件的示例代码。 可以手动创建 sampleEph.js 文件，并运行它来从事件中心接收事件。 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
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

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

可以在[此处](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)找到更多示例。


## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [功能和 Azure 事件中心内的术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)
- 在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) 中查看事件中心的其他 Node.js 示例。
