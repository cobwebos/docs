---
title: 使用 Node.js 发送事件 - Azure 事件中心 | Microsoft Docs
description: 本文提供了一个演练，说明如何创建从 Azure 事件中心发送事件的 Node.js 应用程序。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c75d4404cb0892c3d90261af2fb4982ac84041c4
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163780"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>使用 Node.js 将事件发送到 Azure 事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍了如何从以 Node.js 编写的应用程序中将事件发送到事件中心。

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。
- Visual Studio Code（推荐使用）或任何其他 IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的步骤进行操作，然后继续执行本教程的以下步骤。

按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 本教程后面的步骤将使用此连接字符串。

## <a name="clone-the-sample-git-repository"></a>克隆示例 Git 存储库
将示例 Git 存储库从 [GitHub](https://github.com/Azure/azure-event-hubs-node) 克隆到计算机。 

## <a name="install-nodejs-package"></a>安装 Node.js 程序包
在计算机上安装适用于 Azure 事件中心的 Node.js 程序包。 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>克隆 Git 存储库
从 GitHub 下载或克隆[示例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)。 

## <a name="send-events"></a>发送事件
你克隆的 SDK 包含了多个示例，其中展示了如何使用 node.js 将事件发送到事件中心。 在本快速入门中，你将使用 **simpleSender.js** 示例。 若要观察正在接收的事件，请打开另一个终端，并使用[接收示例](event-hubs-node-get-started-receive.md)来接收事件。

1. 在 Visual Studio Code 中打开项目。 
2. 在 **client** 文件夹下创建一个名为 **.env** 的文件。 从根文件夹中的 **sample.env** 中复制并粘贴示例环境变量。
3. 配置你的事件中心连接字符串、事件中心名称和存储终结点。 可以在 Azure 门户中从“事件中心”页面上的 **RootManageSharedAccessKey** 下的“连接字符串-主要”密钥下复制你的事件中心的连接字符串。 有关详细步骤，请参阅[获取连接字符串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 中，导航到 **client** 文件夹路径。 通过运行以下命令安装节点程序包并生成项目：

    ```nodejs
    npm i
    npm run build
    ```
5. 通过运行以下命令来启动发送事件： 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>查看示例代码 
下面是使用 node.js 将事件发送到事件中心的示例代码。 可以手动创建 sampleSender.js 文件，并运行它来将事件发送到事件中心。 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
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

## <a name="next-steps"></a>后续步骤
在此快速入门中，已使用 Node.js 向事件中心发送消息。 若要了解如何使用 Node.js 从事件中心接收事件，请参阅[从事件中心接收事件 - Node.js](event-hubs-node-get-started-receive.md)

在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) 中查看事件中心的其他 Node.js 示例。
