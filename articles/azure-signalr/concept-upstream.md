---
title: Azure SignalR 服务中的上游设置
description: 介绍了上游设置和上游消息的协议。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: c3e317a87ba888fac3c069cc5327bd89c859e9de
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514231"
---
# <a name="upstream-settings"></a>上游设置

上游是一项预览功能，它允许 Azure SignalR 服务以无服务器模式将消息和连接事件发送到一组终结点。 你可以使用上游在无服务器模式下从客户端调用某个中心方法，并让终结点在客户端建立连接或断开连接时获得通知。

> [!NOTE]
> 只有无服务器模式可以配置上游设置。

## <a name="details-of-upstream-settings"></a>上游设置的详细信息

上游设置由一系列顺序敏感项组成。 每个项都包含：

* 一个 URL 模板，它指定将消息发送到的位置。
* 一组规则。
* 身份验证配置。 

当发生指定的事件时，将按顺序逐个检查项的规则。 消息将发送到第一个匹配项的上游 URL。

### <a name="url-template-settings"></a>URL 模板设置

可以将 URL 参数化以支持各种模式。 有三个预定义的参数：

|预定义的参数|说明|
|---------|---------|
|{hub}| 中心是 Azure SignalR 服务的一个概念。 中心是一个隔离单元。 用户和消息传递的作用域被限定为某个中心。|
|{category}| 类别可以是下列值之一： <ul><li>**连接**：连接生存期事件。 它在客户端建立连接或断开连接时引发。 它包括“已连接”事件和“已断开连接”事件。</li><li>**消息**：当客户端调用某个中心方法时引发。 它包含除“连接”类别中事件之外的所有其他事件。</li></ul>|
|{event}| 对于“消息”类别，事件是客户端发送的[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标。 对于“连接”类别，只使用“已连接”和“已断开连接” 。|

这些预定义参数可用于 URL 模式。 在评估上游 URL 时，参数将替换为某个指定的值。 例如： 
```
http://host.com/{hub}/api/{category}/{event}
```
当“聊天”中心内建立了某个客户端连接时，会向以下 URL 发送消息：
```
http://host.com/chat/api/connections/connected
```
当“聊天”中心内的客户端调用中心方法 `broadcast` 时，会向以下 URL 发送消息：
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>规则设置

你可以分别为“中心规则”、“类别规则”和“事件规则”设置规则。 匹配规则支持三种格式。 以事件规则为例：
- 使用星号 (*) 来匹配任何事件。
- 使用逗号 (,) 来联接多个事件。 例如，`connected, disconnected` 匹配“已连接”和“已断开连接”事件。
- 使用完整的事件名称来匹配事件。 例如，`connected` 匹配“已连接”事件。

> [!NOTE]
> 如果使用 Azure Functions 和 [SignalR 触发器](../azure-functions/functions-bindings-signalr-service-trigger.md)，则 SignalR 触发器将以以下格式公开单个终结点： `https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>` 。
> 只需将 url 模板配置到此 url 即可。

### <a name="authentication-settings"></a>身份验证设置

你可以分别为每个上游设置项配置身份验证。 配置身份验证时，会在上游消息的 `Authentication` 标头中设置令牌。 目前，Azure SignalR 服务支持以下身份验证类型：
- `None`
- `ManagedIdentity`

选择 `ManagedIdentity` 时，必须提前在 Azure SignalR 服务中启用托管标识，可以指定资源。 有关详细信息，请参阅 [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-the-azure-portal"></a>通过 Azure 门户创建上游设置

1. 转到“Azure SignalR 服务”。
2. 选择“设置”并将“服务模式”切换到“无服务器”。 此时会显示上游设置：

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="上游设置":::

3. 在“上游 URL 模式”下添加 URL。 然后，设置（如“中心规则”）会显示默认值。
4. 若要设置“中心规则”、“事件规则”、“类别规则”和“上游身份验证”的设置，请选择“中心规则”的值。     此时会显示一个页面，你可以在其中编辑设置：

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="上游设置详细信息":::

5. 若要设置“上游身份验证”，请确保已先启用托管标识。 然后选择“使用托管标识”。 可以根据需要选择“身份验证资源 ID”下的任何选项。 有关详细信息，请参阅 [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-resource-manager-template"></a>通过资源管理器模板创建上游设置

若要使用 [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)创建上游设置，请在 `properties` 属性中设置 `upstream` 属性。 以下代码片段显示了如何设置用于创建和更新上游设置的 `upstream` 属性。

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>无服务器协议

Azure SignalR 服务将消息发送到遵循以下协议的终结点。

### <a name="method"></a>方法

POST

### <a name="request-header"></a>请求头

|名称 |说明|
|---------|---------|
|X-ASRS-Connection-Id |客户端连接的连接 ID。|
|X-ASRS-Hub |客户端连接所属的中心。|
|X-ASRS-Category |消息所属的类别。|
|X-ASRS-Event |消息所属的事件。|
|X-ASRS-Signature |一个基于哈希的消息身份验证代码 (HMAC)，用于验证。 有关详细信息，请参阅[签名](#signature)。|
|X-ASRS-User-Claims |客户端连接的一组声明。|
|X-ASRS-User-Id |发送消息的客户端的用户标识。|
|X-ASRS-Client-Query |客户端连接到服务时的请求的查询。|
|身份验证 |使用 `ManagedIdentity` 时的一个可选令牌。 |

### <a name="request-body"></a>请求正文

#### <a name="connected"></a>连续

Content-Type: application/json

#### <a name="disconnected"></a>已断开连接

Content-Type：`application/json`

|名称  |类型  |说明  |
|---------|---------|---------|
|错误 |string |已关闭连接的错误消息。 当连接无错关闭时为空。|

#### <a name="invocation-message"></a>调用消息

Content-Type：`application/json` 或 `application/x-msgpack`

|名称  |类型  |说明  |
|---------|---------|---------|
|InvocationId |string | 一个表示调用消息的可选字符串。 可以在[调用](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)中找到详细信息。|
|目标 |string | 与事件相同，并且与[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标相同。 |
|参数 |对象的数组 |一个数组，其中包含要应用于 `Target` 中所引用方法的参数。 |

### <a name="signature"></a>签名

服务将同时使用主要访问密钥和辅助访问密钥作为 `HMAC` 密钥来计算 `X-ASRS-Connection-Id` 值的 SHA256 代码。 向上游发出 HTTP 请求时，服务会在 `X-ASRS-Signature` 标头中设置该代码：
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>后续步骤

- [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)
- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
