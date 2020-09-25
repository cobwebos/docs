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
ms.openlocfilehash: 3c05596e16edc5243b8a97002a5cc5990c69ec43
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943459"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [安装 Python](https://www.python.org/downloads/)
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir chat-quickstart && cd chat-quickstart
```

使用文本编辑器在项目根目录中创建名为“start-chat.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>安装客户端库

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 Python 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端

若要创建聊天客户端，需使用通信服务终结点以及在前提步骤中生成 `Access Token`。 详细了解[用户访问令牌](../../access-tokens.md)。

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `create_chat_thread` 方法创建聊天会话。

- 使用 `topic` 提供一个会话主题；在创建聊天会话后可使用 `update_thread` 函数更新主题。
- 使用 `members` 列出要添加到聊天会话中的 `ChatThreadMember`，`ChatThreadMember` 采用 `CommunicationUser` 类型作为 `user`，这是通过[创建用户](../../access-tokens.md#create-a-user)创建后得到的

响应 `chat_thread_client` 用于在新创建的聊天会话上执行操作，如将成员添加到聊天会话、发送消息、删除消息等。它包含 `thread_id` 属性（聊天会话的唯一 ID）。

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端
get_chat_thread_client 方法返回已存在的会话的会话客户端。 它可用于在创建的会话上执行操作：添加成员、发送消息等，thread_id 是现有聊天会话的唯一 ID。

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `send_message` 方法将消息发送到刚刚创建的由 threadId 标识的聊天会话。

- 使用 `content` 提供聊天消息内容；
- 使用 `priority` 指定消息优先级别，如“正常”或“高”，此属性可用于在应用中为收件人用户提供 UI 指示器，以凸显消息或执行自定义业务逻辑。
- 使用 `senderDisplayName` 指定发送方的显示名称；

响应 `SendChatMessageResult` 包含“id”，它是该消息的唯一 ID。

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔轮询的 `list_messages` 方法来检索聊天消息。

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` 返回最新版本的消息，包括使用 `update_message` 和 `delete_message` 对消息执行的任何编辑或删除。 对于已删除的消息，`ChatMessage.deleted_on` 返回一个日期/时间值，指示删除该消息的时间。 对于已编辑的消息，`ChatMessage.edited_on` 返回一个日期/时间值，指示编辑该消息的日期/时间。 可以使用 `ChatMessage.created_on` 访问消息创建的原始时间，还可使用它对消息进行排序。

`list_messages` 返回可由 `ChatMessage.type` 标识的不同类型的消息。 这些类型包括：

- `Text`：会话成员发送的普通聊天消息。

- `ThreadActivity/TopicUpdate`：指示主题已更新的系统消息。

- `ThreadActivity/AddMember`：指示一个或多个成员已添加到聊天会话的系统消息。

- `ThreadActivity/DeleteMember`：指示已从聊天会话中删除成员的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>将用户作为成员添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在聊天会话中发送消息，以及添加/删除其他成员。 在调用 `add_members` 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

使用 `add_members` 方法将会话成员添加到由 threadId 标识的会话中。

- 使用 `members` 列出要添加到聊天会话的成员；
- `user`（必需）是通过[创建用户](../../access-tokens.md#create-a-user)中的 `CommunicationIdentityClient` 创建的 `CommunicationUser`
- `display_name`（可选）是会话成员的显示名称。
- `share_history_time`（可选）是开始与成员共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或小于会话创建时间的任何日期。 若在添加成员之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为中间日期。

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与添加成员类似，还可以从会话中删除成员。 若要删除，需要跟踪已添加的成员的 ID。

使用 `remove_member` 方法将会话成员从由 threadId 标识的会话中删除。
- `user` 是要从会话中移除的 CommunicationUser。

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `python` 命令运行应用程序。

```console
python start-chat.py
```
