---
title: Azure SignalR 服务中的上游设置
description: 介绍上游设置和上游消息的协议。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: be7736d0c90d1c384e15e8c7dee29d016b052dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559440"
---
# <a name="upstream-settings"></a>上游设置

上游是一项功能，它允许 Azure SignalR 服务以无服务器模式将消息和连接事件发送到一组终结点。 你可以使用上游在无服务器模式下的客户端中调用集线器方法，并让终结点在客户端连接连接或断开连接时获得通知。

> [!NOTE]
> 只有无服务器模式可以配置上游设置。

## <a name="details-of-upstream-settings"></a>上游设置的详细信息

上游设置由顺序敏感的项的列表组成。 每个项包含：

* 一个 URL 模板，该模板指定将消息发送到的位置。
* 一组规则。
* 身份验证配置。 

当发生指定的事件时，将逐个检查项的规则。 消息将发送到第一个匹配项的上游 URL。

### <a name="url-template-settings"></a>URL 模板设置

可以参数化 URL 以支持各种模式。 有三个预定义参数：

|预定义参数|描述|
|---------|---------|
|星| 中心是 Azure SignalR 服务的一种概念。 中心是一种隔离单元。 用户和消息传递的作用域被限制为集线器。|
|类别| 类别可以是以下值之一： <ul><li>**连接**：连接生存期事件。 连接或断开客户端连接时，会触发此情况。 它包括已连接和已断开连接的事件。</li><li>**消息**：在客户端调用集线器方法时激发。 它包括所有其他事件 **，这些事件**除外。</li></ul>|
|引发| 对于 "**消息**" 类别，事件是客户端发送的[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标。 对于 "**连接**" 类别，只使用 "*连接*" 和 "*断开*连接"。|

这些预定义参数可用于 URL 模式。 当你计算上游 URL 时，参数将替换为指定的值。 例如： 
```
http://host.com/{hub}/api/{category}/{event}
```
当 "聊天" 中心中的客户端连接连接时，会将消息发送到此 URL：
```
http://host.com/chat/api/connections/connected
```
当 "聊天" 中心中的客户端调用集线器方法时 `broadcast` ，会将消息发送到此 URL：
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>规则设置

你可以单独设置*中心规则*、*类别规则*和*事件规则*的规则。 匹配规则支持三种格式。 采用事件规则作为示例：
- 使用星号（*）来匹配任何事件。
- 使用逗号（，）来联接多个事件。 例如， `connected, disconnected` 匹配连接事件和已断开连接事件。
- 使用完整的事件名称来匹配事件。 例如， `connected` 匹配连接的事件。

### <a name="authentication-settings"></a>身份验证设置

可以单独为每个上游设置项配置身份验证。 配置身份验证时，会在 `Authentication` 上游消息的标头中设置令牌。 目前，Azure SignalR 服务支持以下身份验证类型：
- `None`
- `ManagedIdentity`

如果选择此 `ManagedIdentity` 项，则必须提前在 Azure SignalR 服务中启用托管标识，并根据需要指定资源。 有关详细信息，请参阅[Azure SignalR Service 的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-the-azure-portal"></a>通过 Azure 门户创建上游设置

1. 请参阅 Azure SignalR 服务。
2. 选择 "**设置**" 并将**服务模式**切换到**无服务器**。 将显示上游设置：

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="上游设置":::

3. 在**上游 URL 模式**下添加 url。 然后，设置（如**中心规则**）将显示默认值。
4. 若要设置**中心规则**、**事件规则**、**类别规则**和**上游身份验证**的设置，请选择 "**中心规则**" 的值。 此时将显示允许编辑设置的页面：

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="上游设置":::

5. 若要设置**上游身份验证**，请确保首先启用了托管标识。 然后选择 "**使用托管标识**"。 根据需要，可以选择 "**身份验证资源 ID**" 下的任何选项。 有关详细信息，请参阅[Azure SignalR Service 的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-resource-manager-template"></a>通过资源管理器模板创建上游设置

若要使用[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)创建上游设置，请设置 `upstream` 属性中的属性 `properties` 。 以下代码片段演示如何设置 `upstream` 用于创建和更新上游设置的属性。

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

|“属性” |描述|
|---------|---------|
|ASRS-Id |客户端连接的连接 ID。|
|ASRS-集线器 |客户端连接所属的中心。|
|ASRS-类别 |消息所属的类别。|
|ASRS-事件 |消息所属的事件。|
|ASRS-签名 |用于验证的基于哈希的消息身份验证代码（HMAC）。 有关详细信息，请参阅[签名](#signature)。|
|ASRS-用户声明 |客户端连接的一组声明。|
|ASRS-用户 Id |发送消息的客户端的用户标识。|
|ASRS-查询 |客户端连接到服务时请求的查询。|
|身份验证 |使用时的可选标记 `ManagedIdentity` 。 |

### <a name="request-body"></a>请求正文

#### <a name="connected"></a>已连接

Content-Type: application/json

#### <a name="disconnected"></a>已断开连接

Content-type：`application/json`

|名称  |类型  |说明  |
|---------|---------|---------|
|错误 |字符串 |已关闭连接的错误消息。 当连接关闭但没有错误时，为空。|

#### <a name="invocation-message"></a>调用消息

Content-type： `application/json` 或`application/x-msgpack`

|名称  |类型  |描述  |
|---------|---------|---------|
|InvocationId |字符串 | 一个表示调用消息的可选字符串。 在[调用](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)中查找详细信息。|
|目标 |字符串 | 与事件相同，与[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标相同。 |
|自变量 |对象的数组 |一个数组，其中包含要应用于在中引用的方法的参数 `Target` 。 |

### <a name="signature"></a>签名

服务将 `X-ASRS-Connection-Id` 使用主访问密钥和辅助访问密钥作为密钥来计算该值的 SHA256 代码 `HMAC` 。 `X-ASRS-Signature`当向上游发出 HTTP 请求时，该服务会在标头中设置它：
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>后续步骤

- [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)
- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
