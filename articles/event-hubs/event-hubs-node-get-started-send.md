---
title: 使用 Node.js 将事件发送到 Azure 事件中心 | Microsoft Docs
description: 使用 Node.js 将事件发送到事件中心入门。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801057"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>使用 Node.js 将事件发送到 Azure 事件中心

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。

若要了解有关事件中心的详细信息，请参阅[事件中心概述](event-hubs-about.md)。

本教程介绍了如何从以 Node.js 编写的应用程序中将事件发送到事件中心。 若要使用 Node.js 事件处理器主机包接收事件，请参阅[相应的接收文章](event-hubs-node-get-started-receive.md)。

[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 上提供了本快速入门的代码。 

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Node.js 版本 8.x 和更高版本。 从 [https://nodejs.org](https://nodejs.org) 下载最新的 LTS 版本。
- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。
- Visual Studio Code（推荐使用）或任何其他 IDE

## <a name="create-a-namespace-and-event-hub"></a>创建命名空间和事件中心
第一步是使用 Azure 门户创建包含事件中心的事件中心命名空间。 如果没有现成可用的，可以按照[使用 Azure 门户创建事件中心命名空间和事件中心](event-hubs-create.md)中的说明创建这些实体。

## <a name="clone-the-sample-git-repository"></a>克隆示例 Git 存储库
将示例 Git 存储库从 [Github](https://github.com/Azure/azure-event-hubs-node) 克隆到计算机。 

## <a name="install-nodejs-package"></a>安装 Node.js 程序包
在计算机上安装适用于 Azure 事件中心的 Node.js 程序包。 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>克隆 Git 存储库
从 Github 下载或克隆[示例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)。 

## <a name="send-events"></a>发送事件
你克隆的 SDK 包含了多个示例，其中展示了如何使用 node.js 将事件发送到事件中心。 在本快速入门中，你将使用 **simpleSender.js** 示例。 若要观察正在接收的事件，请打开另一个终端，并使用[接收示例](event-hubs-node-get-started-receive.md)来接收事件。

1. 在 Visual Studio Code 中打开项目。 
2. 在 **client** 文件夹下创建一个名为 **.env** 的文件。 从根文件夹中的 **sample.env** 中复制并粘贴示例环境变量。
3. 配置你的事件中心连接字符串、事件中心名称和存储终结点。 可以在 Azure 门户中从“事件中心”页面上的 **RootManageSharedAccessKey** 下的“连接字符串-主要”密钥下复制你的事件中心的连接字符串。 有关详细步骤，请参阅[获取连接字符串](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace)。
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

若要了解有关事件中心的详细信息，请参阅以下文章：

* [使用 Node.js 接收事件](event-hubs-node-get-started-receive.md)
* [GitHub 上的示例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [事件中心常见问题解答](event-hubs-faq.md)

<!-- Links -->
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
