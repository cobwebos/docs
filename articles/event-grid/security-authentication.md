---
title: Azure 事件网格安全和身份验证
description: 介绍 Azure 事件网格及其概念。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: babanisa
ms.openlocfilehash: ec34b9c5f78b4d0ea59382f616fab88e0e1dedc9
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730928"
---
# <a name="event-grid-security-and-authentication"></a>事件网格安全和身份验证 

Azure 事件网格包含三种类型的身份验证：

* WebHook 事件传送
* 事件订阅
* 自定义主题发布

## <a name="webhook-event-delivery"></a>WebHook 事件传送

Webhook 是从 Azure 事件网格接收事件的多种方式之一。 当新事件准备就绪时，事件网格服务会向已配置的终结点 POST HTTP 请求，并在请求正文中包含该事件。

与众多支持 Webhook 的其他服务一样，事件网格需要你证明对 Webhook 的所有权，然后才能开始向该终结点传送事件。 此要求可防止恶意用户用大量事件淹没你的终结点。 如果使用下面列出的三项 Azure 服务中的任何一项，Azure 基础结构将自动进行此验证：

* 使用[事件网格连接器](https://docs.microsoft.com/connectors/azureeventgrid/)的 Azure 逻辑应用
* 通过 [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) 实现 Azure 自动化
* 使用[事件网格触发器](../azure-functions/functions-bindings-event-grid.md)的 Azure Functions

如果使用其他任何类型的终结点（例如基于 HTTP 触发器的 Azure 函数），终结点代码需要参与事件网格的验证握手。 事件网格支持通过两种方式来验证订阅。

1. **ValidationCode 握手（编程式）**：如果你控制终结点的源代码，建议使用此方式。 在创建事件订阅时，事件网格会将一个订阅验证事件发送到终结点。 此事件的架构与任何其他事件网格事件类似。 此事件的数据部分包括一个 `validationCode` 属性。 你的应用程序将确认验证请求是针对预期的事件订阅的，并将验证码回送给事件网格。 所有事件网格版本都支持此握手机制。

2. **ValidationURL 握手（手动）**：在某些情况下，无法访问终结点的源代码来实现 ValidationCode 握手。 如果使用第三方服务（例如 [Zapier](https://zapier.com) 或 [IFTTT](https://ifttt.com/)），则无法以编程方式使用验证码进行响应。

   从版本 2018-05-01-preview 开始，事件网格支持手动验证握手。 如果你在创建事件订阅时使用的 SDK 或工具使用了 API 版本 2018-05-01-preview 或更高版本，则事件网格将在订阅验证事件的数据部分中发送 `validationUrl` 属性。 若要完成握手，请在事件数据中找到该 URL 并向其发送一个 GET 请求。 你可以使用 REST 客户端或 Web 浏览器。

   所提供的 URL 的有效期为 10 分钟。 在该时间内，事件订阅的预配状态为 `AwaitingManualAction`。 如果在 10 分钟内未完成手动验证，则配置状态被设为 `Failed`。 你将必须在开始手动验证之前重新创建事件订阅。

### <a name="validation-details"></a>验证详细信息

* 在创建/更新事件订阅时，事件网格会将一个订阅验证事件发送到目标终结点。 
* 该事件包含标头值“aeg-event-type:SubscriptionValidation”。
* 事件正文具有与其他事件网格事件相同的架构。
* 该事件的 eventType 属性是 `Microsoft.EventGrid.SubscriptionValidationEvent`。
* 该事件的 data 属性包括一个 `validationCode` 属性，其中含有随机生成的字符串。 例如，“validationCode: acb13…”。
* 事件数据还包括 `validationUrl` 属性，其中包含用于手动验证订阅的 URL。
* 该数组仅包含验证事件。 你回显验证代码后，事件网格会以单独的请求发送其他事件。
* EventGrid DataPlane SDK 包含对应订阅验证事件数据和订阅验证响应的类。

以下示例显示了 SubscriptionValidationEvent 示例：

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

为证明终结点所有权，请在 validationResponse 属性中回显 验证代码，如下例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

你必须返回 HTTP 200 OK 响应状态代码。 HTTP 202 Accepted 未被识别为有效的事件网格订阅验证响应。

另外，还可以通过将 GET 请求发送到验证 URL 来手动验证订阅。 事件订阅将一直处于挂起状态，直到得到验证。

有关处理订阅验证握手的示例，请参阅 [C# 示例](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)。

### <a name="checklist"></a>清单

创建事件订阅期间，是否看到“尝试验证所提供的终结点 https://your-endpoint-here 失败。 有关更多详细信息，请访问 https://aka.ms/esvalidation ”等错误消息，它表示验证握手出现故障。 若要解决此错误，请验证以下各方面：

* 能否控制目标终结点中的应用程序代码？ 例如，如果正在编写基于 HTTP 触发器的 Azure 函数，是否有权访问应用程序代码，以对其进行更改？
* 如果有权访问应用程序代码，请实现基于 ValidationCode 的握手机制，如上面的示例中所示。

* 如果无权访问应用程序代码（例如，如果使用的是支持 Webhook 的第三方服务），则可以使用手动握手机制。 请确保使用 2018-05-01-preview API 版本或更高版本（安装事件网格 Azure CLI 扩展）以便接收验证事件中的 validationUrl。 若要完成手动验证握手，请获取 `validationUrl` 属性的值，并在 Web 浏览器中访问该 URL。 如果验证成功，应当会在 Web 浏览器中看到指明验证已成功的消息。 你将看到事件订阅的 provisioningState 为“Succeeded”。 

### <a name="event-delivery-security"></a>事件传递安全性

在创建事件订阅时，可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 将这些查询参数之一设置为某个机密，例如[访问令牌](https://en.wikipedia.org/wiki/Access_token)。 Webhook 可以使用该机密来识别事件是否来自具有有效权限的事件网格。 事件网格会在前往 Webhook 的每个事件传递中包括这些查询参数。

编辑事件订阅时，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用了 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 参数，否则，不会显示或返回查询参数。

最后，请务必注意 Azure 事件网格仅支持 HTTPS Webhook 终结点。

## <a name="event-subscription"></a>事件订阅

若要订阅事件，必须证明你有权访问事件源和处理程序。 上一部分中假定你拥有一个 WebHook。 如果你使用不是 WebHook 的事件处理程序（例如事件中心或队列存储），则需要对该资源具有写入访问权限。 此权限检查可防止未经授权的用户向你的资源发送事件。

你必须在作为事件源的资源上具有 **Microsoft.EventGrid/EventSubscriptions/Write** 权限。 因为要在资源范围内写入新的订阅，所以需要此权限。 所需资源因是订阅系统主题还是订阅自定义主题而异。 本部分介绍了这两种类型。

### <a name="system-topics-azure-service-publishers"></a>系统主题（Azure 服务发布服务器）

对于系统主题，需要在资源范围内写入新事件订阅的权限，才能发布该事件。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

例如，若要订阅存储帐户上名为“myacct”的事件，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限

### <a name="custom-topics"></a>自定义主题

对于自定义主题，需要在事件网格主题范围内写入新事件订阅的权限。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

例如，若要订阅名为“mytopic”的自定义主题，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限

## <a name="custom-topic-publishing"></a>自定义主题发布

自定义主题使用共享访问签名 (SAS) 或密钥身份验证。 建议使用 SAS，但密钥身份验证提供简单的编程，并与多个现有 webhook 发布服务器兼容。 

HTTP 标头中包括身份验证值。 对于 SAS，使用 aeg-sas-token 作为标头值。 对于密钥身份验证，使用 aeg-sas-key 作为标头值。

### <a name="key-authentication"></a>密钥身份验证

密钥身份验证是最简单的身份验证形式。 使用以下格式：`aeg-sas-key: <your key>`

例如，使用以下项传递密钥：

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 令牌

事件网格的 SAS 令牌包括资源、过期时间和签名。 SAS 令牌的格式是：`r={resource}&e={expiration}&s={signature}`。

资源是要将事件发送到的事件网格主题的路径。 例如，有效的资源路径是：`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

从密钥生成签名。

例如，有效的 aeg-sas-token 值是：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

以下示例会创建用于事件网格的 SAS 令牌：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>管理访问控制

借助 Azure 事件网格，可以控制授予不同用户用来执行各种管理操作的访问级别，例如列出事件订阅、创建新的事件订阅及生成密钥。 事件网格使用 Azure 的基于角色的访问控制 (RBAC)。

### <a name="operation-types"></a>操作类型

事件网格支持下列操作：

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

最后三个操作可能会返回从常规读取操作中筛选出的机密信息。 建议限制对这些操作的访问。 

### <a name="built-in-roles"></a>内置角色

事件网格提供了用于管理事件订阅的两个内置角色。 它们在实施[事件域](event-domains.md)时非常重要，因为它们为用户提供了订阅事件域中主题所需的权限。 这些角色专注于事件订阅，不授予对创建主题等操作的访问权限。

你可以[将这些角色分配给用户或组](../role-based-access-control/quickstart-assign-role-user-portal.md)。

**EventGrid EventSubscription 参与者（预览）**：管理事件网格订阅操作

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription 读者（预览）**：读取事件网格订阅

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>自定义角色

如果需要指定不同于内置角色的权限，可以创建自定义角色。

下面是允许用户采取不同操作的示例事件网格角色定义。 这些自定义角色与内置角色不同，因为它们授予比只是事件订阅更广泛的访问权限。

**EventGridReadOnlyRole.json**：仅允许只读操作。

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**：允许受限制的发布操作但禁止删除操作。

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**：允许所有事件网格操作。

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

可以使用 [PowerShell](../role-based-access-control/custom-roles-powershell.md)、[Azure CLI](../role-based-access-control/custom-roles-cli.md) 和 [REST](../role-based-access-control/custom-roles-rest.md) 创建自定义角色。

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)
