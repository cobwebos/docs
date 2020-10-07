---
title: include 文件
description: include 文件
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943492"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务管理客户端库。

```console

npm install @azure/communication-administration --save

```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

## <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开新文本文件
1. 添加 `require` 调用以加载 `CommunicationIdentityClient`
1. 为程序创建结构，包括基本的异常处理

使用以下代码以开始执行以下操作：

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 将新文件另存为 user-tokens-quickstart 目录中的 issue-token.js。

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 `main` 方法中：

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>创建用户

Azure 通信服务维护轻量级标识目录。 使用 `createUser` 方法可在目录中创建具有唯一 `Id` 的新项。 应维护应用程序的用户与通信服务生成的标识之间的映射（例如，通过将它们存储在应用程序服务器的数据库中）。

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>颁发用户访问令牌

使用 `issueToken` 方法为通信服务用户颁发访问令牌。 如果未提供可选的 `user` 参数，将创建一个新用户并将其与令牌一起返回。

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

用户访问令牌是需要重新颁发的短期凭据，以防止用户遇到服务中断的情况。 `expiresOn` 响应属性指示令牌的生存期。

## <a name="revoke-user-access-tokens"></a>调用用户访问令牌

在某些情况下，你可能需要显式撤销用户访问令牌，例如，当用户更改用于向服务进行身份验证的密码时。 这将使用 `revokeTokens` 方法使用户的所有访问令牌无效。

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>删除用户

删除用户将撤销所有活动令牌，并阻止你为标识颁发后续令牌。 它还会删除与用户关联的所有保存的内容。

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>运行代码

在控制台提示符下，导航到包含 issue-token.js 文件的目录，然后执行以下 `node` 命令来运行应用。

```console
node ./issue-token.js
```
