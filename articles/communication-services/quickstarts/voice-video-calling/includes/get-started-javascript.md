---
title: 快速入门 - 使用 Azure 通信服务向 Web 应用添加 VOIP 呼叫
description: 本教程介绍如何使用适用于 Javascript 的 Azure 通信服务呼叫客户端库
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d58b4d86936c56a08f27bef59edc1d3cc4ce4617
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943952"
---
本快速入门将介绍如何使用适用于 JavaScript 的 Azure 通信服务呼叫客户端库开始呼叫。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于实例化呼叫客户端的用户访问令牌。 了解如何[创建和管理用户访问令牌](../../access-tokens.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir calling-quickstart && cd calling-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫客户端库。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

### <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 webpack、 webpack-cli 和 webpack-dev-server npm 包，并将它们作为 package.json 中的开发依赖项列出：

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

在项目的根目录中创建一个 index.html 文件。 我们将使用此文件来配置基本布局，该布局允许用户呼叫 Azure 通信机器人。

代码如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。 添加以下代码以导入呼叫客户端，并获取对 DOM 元素的引用，以便我们可以附加业务逻辑。 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                             | 说明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是呼叫客户端库的主入口点。                                                                       |
| CallAgent                        | CallAgent 用于启动和管理呼叫。                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential 类实现用于实例化 CallAgent 的 CommunicationUserCredential 接口。 |


## <a name="authenticate-the-client"></a>验证客户端

需要将 `<USER_ACCESS_TOKEN>` 替换为资源的有效用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../access-tokens.md)文档。 使用 `CallClient`，通过 `CommunicationUserCredential` 初始化 `CallAgent` 实例，这将使我们能够启动和接收呼叫。 将下面的代码添加到 client.js：

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callClient.createCallAgent(tokenCredential).then(agent => {
  callAgent = agent;
  callButton.disabled = false;
});
```

## <a name="start-a-call"></a>开始呼叫

添加事件处理程序，以便在单击 `callButton` 时启动呼叫：

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>结束呼叫

添加事件侦听器，以便在单击 `hangUpButton` 时结束当前呼叫：

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone` 属性结束所有呼叫参与者的呼叫。

## <a name="run-the-code"></a>运行代码

使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 webserver 上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

可以通过在“文本”字段中提供用户 ID 并单击“开始呼叫”按钮，启动出站 VOIP 呼叫。 呼叫 `8:echo123` 会将你连接到回显机器人，这对于入门和验证音频设备是否正常运行非常有用。
