---
title: include 文件
description: include 文件
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377305"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安装[Node.js](https://nodejs.org/en/download/)活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 需要为此快速入门记录资源终结点。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-web-application"></a>新建 Web 应用程序

首先，打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

使用文本编辑器在项目根目录中创建名为 start-chat.js 的文件。 将在以下部分中将此快速入门的所有源代码添加到此文件。

### <a name="install-the-packages"></a>安装包

使用 `npm install` 命令安装适用于 Javascript 的以下通信服务客户端库。

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

### <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 webpack、 webpack-cli 和 webpack-dev-server npm 包，并将它们作为 package.json 中的开发依赖项列出：

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

在项目的根目录中创建一个 index.html 文件。 我们将使用此文件作为模板，使用适用于 JavaScript 的 Azure 通信聊天客户端库添加聊天功能。

代码如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。 

### <a name="create-a-chat-client"></a>创建聊天客户端

若要在你的 Web 应用中创建聊天客户端，你将使用通信服务终结点和作为前提步骤的一部分而生成的访问令牌。 用户访问令牌使你可以构建直接对 Azure 通信服务进行身份验证的客户端应用程序。 在服务器上生成这些令牌后，将它们传回客户端设备。 需要使用 `Common client library` 中的 `AzureCommunicationUserCredential` 类将令牌传递给聊天客户端。

在项目的根目录中创建一个 client.js 文件。 我们将使用此文件通过适用于 JavaScript 的 Azure 通信聊天客户端库添加聊天功能。

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
将终结点替换为之前基于[创建 Azure 通信资源](../../create-communication-resource.md)文档创建的终结点。
将 USER_ACCESS_TOKEN 替换为基于[用户访问令牌](../../access-tokens.md)文档颁发的令牌。
将此代码添加到 client.js 文件


### <a name="run-the-code"></a>运行代码
使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 webserver 上捆绑应用程序主机：
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
打开浏览器并导航到 http://localhost:8080/。
在浏览器中的“开发人员工具”控制台中，应会显示以下内容：

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>对象模型 
以下类和接口处理适用于 JavaScript 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知以及阅读回执和订阅聊天事件。 |


## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `createThread` 方法创建聊天会话。

`createThreadRequest` 用于描述会话请求：

- 使用 `topic` 为该聊天提供一个主题；在创建聊天会话后可使用 `UpdateThread` 函数更新主题。 
- 使用 `members` 列出要添加到聊天会话的成员；

解决后，`createChatThread` 方法将返回 `threadId`，用于在新创建的会话上执行操作，如将成员添加到聊天会话、发送消息、删除消息等。

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

将 USER_ID_FOR_JACK 和 USER_ID_FOR_GEETA 替换为从上一步（创建用户并颁发[用户访问令牌](../../access-tokens.md)）获得的用户 ID 

刷新浏览器选项卡时，控制台中应会显示以下内容
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`getChatThreadClient` 方法返回已存在的会话的 `chatThreadClient`。 它可用于在创建的会话上执行操作：添加成员、发送消息等，threadId 是现有聊天会话的唯一 ID。

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
添加此代码以代替 client.js 中的 `<CREATE CHAT THREAD CLIENT>` 注释，刷新浏览器选项卡并检查控制台，你应该会看到：
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `sendMessage` 方法将聊天消息发送到刚刚创建的由 threadId 标识的会话。

`sendMessageRequest` 描述聊天消息请求的必填字段：

- 使用 `content` 提供聊天消息内容；

`sendMessageOptions` 描述聊天消息请求的可选字段：

- 使用 `priority` 指定聊天消息优先级别，如“正常”或“高”，此属性可用于在应用中为接收人用户提供 UI 指示器，以凸显消息或执行自定义业务逻辑。   
- 使用 `senderDisplayName` 指定发送方的显示名称；

响应 `sendChatMessageResult` 包含“id”，它是该消息的唯一 ID。

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
添加此代码以代替 client.js 中的 `<SEND MESSAGE TO A CHAT THREAD>` 注释，刷新浏览器选项卡并检查控制台。
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

使用实时信令，你可以订阅以侦听新的传入消息并相应更新内存中的当前消息。 Azure 通信服务支持[你可以订阅的事件列表](../../../concepts/chat/concepts.md#real-time-signaling)。

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
添加此代码以代替 client.js 中的 `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` 注释。
刷新浏览器选项卡，控制台中应会显示消息 `Notification chatMessageReceived`；

或者可以通过按指定间隔轮询 `listMessages` 方法来检索聊天消息。 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
添加此代码以代替 client.js 中的 `<LIST MESSAGES IN A CHAT THREAD>` 注释。
刷新选项卡，你应该可以在控制台中找到在这个聊天会话中发送的消息列表。


`listMessages` 返回最新版本的消息，包括使用 `updateMessage` 和 `deleteMessage` 对消息执行的任何编辑或删除。
对于已删除的消息，`chatMessage.deletedOn` 返回一个日期/时间值，指示删除该消息的时间。 对于已编辑的消息，`chatMessage.editedOn` 返回一个日期/时间值，指示编辑该消息的日期/时间。 可以使用 `chatMessage.createdOn` 访问消息创建的原始时间，还可使用它对消息进行排序。

`listMessages` 返回可由 `chatMessage.type` 标识的不同类型的消息。 这些类型包括：

- `Text`：会话成员发送的普通聊天消息。

- `ThreadActivity/TopicUpdate`：指示主题已更新的系统消息。

- `ThreadActivity/AddMember`：指示一个或多个成员已添加到聊天会话的系统消息。

- `ThreadActivity/RemoveMember`：指示已从聊天会话中删除成员的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>将用户作为成员添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在聊天会话中发送消息，以及添加/删除其他成员。 在调用 `addMembers` 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

`addMembersRequest` 描述请求对象，其中 `members` 列出要添加到聊天会话的成员；
- `user`（必选）是要添加到聊天会话的通信用户。
- `displayName`（可选）是会话成员的显示名称。
- `shareHistoryTime`（可选）是开始与成员共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或小于会话创建时间的任何日期。 若在添加成员之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为所选日期。

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
将 NEW_MEMBER_USER_ID 替换为[新用户 ID](../../access-tokens.md)。在 client.js 中添加此代码以代替 `<ADD NEW MEMBER TO THREAD>` 注释 

## <a name="list-users-in-a-chat-thread"></a>列出聊天会话中的用户
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
添加此代码以代替 client.js 中的 `<LIST MEMBERS IN A THREAD>` 注释，刷新浏览器选项卡并检查控制台，你应该会看到会话中用户的信息。

## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与添加成员类似，可以从聊天会话中删除成员。 若要删除，需要跟踪已添加的成员的 ID。

使用 `removeMember` 方法，其中 `member` 是要从会话中删除的通信用户。

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
将 MEMBER_ID 替换为在上一步中使用的用户 ID(<NEW_MEMBER_USER_ID>)。
添加此代码以代替 client.js 中的 `<REMOVE MEMBER FROM THREAD>` 注释，
