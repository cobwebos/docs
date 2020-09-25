---
title: 可信服务 JavaScript
description: 这是为通信服务创建可信服务的 JavaScript 版本。
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d4ef8baa123f805d380b14fa24abff65903cb41d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943367"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 10.14.1）。 可以使用 `node --version` 命令检查你的版本。 
- Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。

## <a name="overview"></a>概述

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="可信服务体系结构的关系图":::

对于本教程，我们将创建一个 Azure 函数，它将用作可信令牌预配服务。 你可以使用本教程来启动自己的令牌预配服务。

此服务负责对 Azure 通信服务的用户进行身份验证。 通信服务应用程序的用户需要 `Access Token` 才能参与聊天线程和 VoIP 呼叫。 Azure 函数将充当用户与通信服务之间的可信中转站。 这样，你便可以预配访问令牌，而无需向用户公开资源连接字符串。

有关详细信息，请参阅[客户端-服务器体系结构](../../concepts/client-and-server-architecture.md)以及[身份验证和授权](../../concepts/authentication.md)概念文档。

## <a name="setting-up"></a>设置

### <a name="azure-functions-set-up"></a>Azure Functions 设置

首先设置 Azure 函数的基本结构。 可在此处可以找到有关设置的分步说明：[使用 Visual Studio Code 创建函数](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)

我们的 Azure 函数需要以下配置：

- Language：JavaScript
- 模板：HTTP 触发器
- 授权级别：匿名（如果你更偏好不同的授权模型，则可以在以后进行切换）
- 函数名称：用户定义

按照 [Azure Functions 说明](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)进行以上配置之后，你应在 Visual Studio Code 中获得一个用于 Azure 函数的项目，以及包含函数本身的 `index.js` 文件。 此文件内的代码应如下所示：

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

现在，我们将继续安装 Azure 通信服务库。

### <a name="install-communication-services-libraries"></a>安装通信服务库

我们将使用 `Administration` 库生成 `User Access Tokens`。

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务管理客户端库。

```console

npm install @azure/communication-administration --save

```

`--save` 选项在 package.json 文件中列出作为依赖项的库。

在 `index.js` 文件的顶部，导入 `CommunicationIdentityClient` 的接口

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>访问令牌生成

若要允许我们的 Azure 函数生成 `User Access Tokens`，首先需要使用通信服务资源的连接字符串。

有关检索连接字符串的详细信息，请访问[资源预配快速入门](../../quickstarts/create-communication-resource.md)。

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

接下来，我们将修改原始函数以生成 `User Access Tokens`。 

`User Access Tokens` 通过从 `createUser` 方法创建用户来生成。 创建用户后，便可以使用 `issueToken` 方法为该用户生成 Azure 函数返回的令牌。

对于此示例，我们将令牌范围配置为 `voip`。 你的应用程序可能需要其他范围。 详细了解[范围](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

对于现有通信服务 `CommunicationUser`，可以跳过创建步骤，只生成访问令牌。 可在[创建用户访问令牌快速入门](../../quickstarts/access-tokens.md)中找到更多详细信息。

## <a name="test-the-azure-function"></a>测试 Azure 函数

使用 `F5` 在本地运行 Azure 函数。 这会在本地初始化 Azure 函数并使其可通过以下方式进行访问：`http://localhost:7071/api/FUNCTION_NAME`。 查看有关[在本地运行](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-locally)的其他文档

在浏览器中打开 URL，应会看到包含通信用户 Id、令牌和令牌过期时间的响应正文。

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="显示创建的 Azure 函数的响应示例的屏幕截图。":::

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

若要部署 Azure 函数，可以按照[分布说明](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#sign-in-to-azure)进行操作

一般而言，需要：
1. 从 Visual Studio 登录 Azure
2. 将项目发布到 Azure 帐户。 此处需要选择现有订阅。
3. 使用 Visual Studio 向导创建新 Azure 函数资源，或是使用现有资源。 对于新资源，需要将其配置到所需区域、运行时和唯一标识符。
4. 等待部署完成
5. 运行函数 🎉

## <a name="run-azure-function"></a>运行 Azure 函数

使用 url `http://<function-appn-ame>.azurewebsites.net/api/<function-name>` 运行 Azure 函数

可以通过在 Visual Studio Code 中右键单击函数并复制函数 URL 来找到 URL。

有关[运行 Azure 函数](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-in-azure)的详细信息
