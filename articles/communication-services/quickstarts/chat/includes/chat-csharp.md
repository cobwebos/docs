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
ms.openlocfilehash: 1995691e276ea699b0b4b61b2a88c030f3d58b7d
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779570"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：
- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安装 [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `ChatQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o ChatQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

安装适用于 .NET 的 Azure 通信聊天客户端库

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.2
``` 

## <a name="object-model"></a>对象模型

以下类处理适用于 C# 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端

为创建聊天客户端，需要使用通信服务终结点和作为前提步骤的一部分而生成的访问令牌。 需要使用 `Administration` 客户端库的 `CommunicationIdentityClient` 类来创建用户，并颁发要传递到聊天客户端的令牌。 详细了解[用户访问令牌](../../access-tokens.md)。

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `createChatThread` 方法创建聊天会话。
- 使用 `topic` 为该聊天提供一个主题；在创建聊天会话后可使用 `UpdateThread` 函数更新主题。
- 使用 `members` 属性传递要添加到聊天会话的 `ChatThreadMember` 对象的列表。 使用 `CommunicationUser` 对象初始化 `ChatThreadMember` 对象。 若要获取 `CommunicationUser` 对象，需要传递一个按照[创建用户](../../access-tokens.md#create-a-user)中的说明创建的访问 ID

响应 `chatThreadClient` 用于在创建的聊天会话上执行操作：将成员添加到聊天会话、发送消息、删除消息等。它包含 `Id` 属性（聊天会话的唯一 ID）。 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端
`GetChatThreadClient` 方法返回某个已存在的会话的会话客户端。 它可用于在创建的会话上执行操作：添加成员、发送消息等，threadId 是现有聊天会话的唯一 ID。

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = await chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `SendMessage` 方法将消息发送到由 threadId 标识的会话。

- 使用 `content` 提供聊天消息内容，这是必需的。
- 使用 `priority` 指定消息优先级，如“普通”或“高”，如果未指定，将使用“普通”。
- 使用 `senderDisplayName` 指定发送方的显示名称，如果未指定，将使用空名称。

`SendChatMessageResult` 是发送消息后返回的响应，它包含一个 ID，该 ID 是消息的唯一 ID。

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔在聊天会话客户端上轮询 `GetMessages` 方法来检索聊天消息。

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` 采用可选的 `DateTimeOffset` 参数。 如果指定了该偏移量，你将收到在其之后接收、更新或删除的消息。 请注意，还会返回在偏移时间之前收到但在该时间之后编辑过或删除的消息。

`GetMessages` 返回最新版本的消息，包括使用 `UpdateMessage` 和 `DeleteMessage` 对消息执行的任何编辑或删除。 对于已删除的消息，`chatMessage.DeletedOn` 返回日期/时间值，该值指示该消息删除的时间。 对于已编辑的消息，`chatMessage.EditedOn` 返回指示编辑该消息的日期/时间。 可以使用 `chatMessage.CreatedOn` 访问消息创建的原始时间，还可使用它对消息进行排序。

`GetMessages` 返回可使用 `chatMessage.Type` 标识的不同类型的消息。 这些类型包括：

- `Text`：会话成员发送的普通聊天消息。

- `ThreadActivity/TopicUpdate`：指示主题已更新的系统消息。

- `ThreadActivity/AddMember`：指示一个或多个成员已添加到聊天会话的系统消息。

- `ThreadActivity/DeleteMember`：指示已从聊天会话中删除成员的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="update-a-message"></a>更新消息

可以通过调用 `ChatThreadClient` 上的 `UpdateMessage` 来更新已发送的消息。

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>删除消息

可以通过调用 `ChatThreadClient` 上的 `DeleteMessage` 来删除消息。

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>将用户作为成员添加到聊天会话

创建会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在会话中发送消息，以及添加/删除其他成员。 在调用 `AddMembers` 之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

使用 `AddMembers` 方法将会话成员添加到由 threadId 标识的会话中。

 - 使用 `members` 列出要添加到聊天会话的成员；
 - `User`（必需）是为此新用户获取的标识。
 - `DisplayName`（可选）是会话成员的显示名称。
 - `ShareHistoryTime`（可选）是开始与成员共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将其设置为 DateTime.MinValue。 若不共享任何历史记录，请在添加成员之前将其设置为当前时间。 若要共享部分历史记录，请将其设置为会话创建时间和当前时间之间的时间点。

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与将用户添加到会话类似，可以从聊天会话中删除用户。 若要执行此操作，需要跟踪已添加的成员的标识 (CommunicationUser)。

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```
