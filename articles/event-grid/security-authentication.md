---
title: Azure 事件网格安全和身份验证
description: 介绍 Azure 事件网格及其概念。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: babanisa
ms.openlocfilehash: 257f7cbd20d21903f4cf7daf68b5f185d0af10bc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965443"
---
# <a name="event-grid-security-and-authentication"></a>事件网格安全和身份验证 

Azure 事件网格包含三种类型的身份验证：

* 事件订阅
* 事件发布
* WebHook 事件传送

## <a name="webhook-event-delivery"></a>WebHook 事件传送

Webhook 是从 Azure 事件网格接收事件的多种方式之一。 当新事件准备就绪时，EventGrid 服务会向已配置的终结点 POST HTTP 请求，并在请求正文中包含该事件。

与众多支持 Webhook 的其他服务一样，EventGrid 需要你证明对 Webhook 的“所有权”，然后才能开始向该终结点传送事件。 此要求是为了防止意外的终结点成为来自 EventGrid 的事件传送的目标终结点。 但是，如果使用下面列出的三项 Azure 服务中的任何一项，Azure 基础结构将自动进行此验证：

* Azure 逻辑应用，
* Azure 自动化，
* 适用于 EventGrid 的 Azure Functions 触发器。

如果使用其他任何类型的终结点（例如基于 HTTP 触发器的 Azure 函数），终结点代码需要参与 EventGrid 的验证握手。 EventGrid 支持两种不同的验证握手模型：

1. **ValidationCode 握手**：创建事件订阅时，EventGrid 将向终结点 POST“订阅验证事件”。 此事件的架构类似于其他任何 EventGridEvent，并且此事件的数据部分包括 `validationCode` 属性。 应用程序确认验证请求针对预期的事件订阅后，应用程序代码需要通过将验证代码回显回 EventGrid 以进行响应。 所有 EventGrid 版本均都支持此握手机制。

2. **ValidationURL 握手（手动握手）**：在某些情况下，可能无法控制终结点源代码，以便能够实现基于 ValidationCode 的握手。 如果使用第三方服务（例如 [Zapier](https://zapier.com) 或 [IFTTT](https://ifttt.com/)），可能无法以编程方式使用验证码回应。 因此，从版本 2018-05-01-preview 开始，EventGrid 现在支持手动验证握手。 如果要使用此新的 API 版本 (2018-05-01-preview) 的 SDK/工具创建事件订阅，EventGrid 将发送 `validationUrl` 属性（以及 `validationCode` 属性）作为订阅验证事件数据部分的一部分。 若要完成握手，只需通过 REST 客户端或使用 Web 浏览器对该 URL 执行 GET 请求。 提供的验证 URL 有效时间仅 10 分钟左右。 在该时间内，事件订阅的预配状态为 `AwaitingManualAction`。 如果在 10 分钟内未完成手动验证，则配置状态被设为 `Failed`。 再次尝试手动验证前，必须重新尝试创建此事件订阅。

“手动验证”机制处于预览状态。 若要使用它，必须安装用于 [Azure CLI](/cli/azure/install-azure-cli) 的[事件网格扩展](/cli/azure/azure-cli-extensions-list)。 可使用 `az extension add --name eventgrid` 进行安装。 如果使用 REST API，请确保使用 `api-version=2018-05-01-preview`。

### <a name="validation-details"></a>验证详细信息

* 在创建/更新事件订阅时，事件网格会将“订阅验证”事件发送到目标终结点。 
* 该事件包含标头值“aeg-event-type: SubscriptionValidation”。
* 事件正文具有与其他事件网格事件相同的架构。
* 该事件的 eventType 属性是“Microsoft.EventGrid.SubscriptionValidationEvent”。
* 事件数据属性包括“validationCode”属性，其中含有随机生成的字符串。 例如，“validationCode: acb13…”。
* 如果使用 API 版本 2018-05-01-preview，事件数据还将包括 `validationUrl` 属性，其中包含用于手动验证订阅的 URL。
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

或者，可以通过将 GET 请求发送到验证 URL 来手动验证订阅。 事件订阅将一直处于挂起状态，直到得到验证。

若要查找演示如何处理订阅验证握手的 C# 示例，请访问 https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs。

### <a name="checklist"></a>清单

创建事件订阅期间，是否看到“尝试验证所提供的终结点 https://your-endpoint-here 失败。 有关更多详细信息，请访问 https://aka.ms/esvalidation ”等错误消息，它表示验证握手出现故障。 若要解决此错误，请验证以下各方面：

* 能否控制目标终结点中的应用程序代码？ 例如，如果正在编写基于 HTTP 触发器的 Azure 函数，是否有权访问应用程序代码，以对其进行更改？
* 如果有权访问应用程序代码，请实现基于 ValidationCode 的握手机制，如上面的示例中所示。

* 如果无权访问应用程序代码（例如使用的是支持 Webhook 的第三方服务），则可以使用手动握手机制。 为了执行此操作，请确保使用 2018-05-01-preview API 版本（例如，使用上述 EventGrid CLI 扩展）以便接收验证事件中的 validationUrl。 若要完成手动验证握手，请获取“validationUrl”属性值，并在 Web 浏览器中访问该 URL。 如果验证成功，应在 Web 浏览器中看到表示验证成功的消息，并且事件订阅的 provisioningState 为“已成功”。 

### <a name="event-delivery-security"></a>事件传递安全性

在创建事件订阅时，可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 将这些查询参数之一设置为机密（例如[访问令牌](https://en.wikipedia.org/wiki/Access_token)），Webhook 可以使用它来确认事件来自具有有效权限的事件网格。 事件网格会在前往 Webhook 的每个事件传递中包括这些查询参数。

编辑事件订阅时，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用了 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 参数，否则，不会显示或返回查询参数。

最后，请务必注意 Azure 事件网格仅支持 HTTPS Webhook 终结点。

## <a name="event-subscription"></a>事件订阅

若要订阅事件，必须具有所需资源的 Microsoft.EventGrid/EventSubscriptions/Write 权限。 因为要在资源范围内写入新的订阅，所以需要此权限。 所需资源因是订阅系统主题还是订阅自定义主题而异。 本部分介绍了这两种类型。

### <a name="system-topics-azure-service-publishers"></a>系统主题（Azure 服务发布服务器）

对于系统主题，需要在资源范围内写入新事件订阅的权限，才能发布该事件。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

例如，若要订阅存储帐户上名为“myacct”的事件，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限

### <a name="custom-topics"></a>自定义主题

对于自定义主题，需要在事件网格主题范围内写入新事件订阅的权限。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

例如，若要订阅名为“mytopic”的自定义主题，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限

## <a name="topic-publishing"></a>主题发布

主题使用共享访问签名 (SAS) 或密钥身份验证。 建议使用 SAS，但密钥身份验证提供简单的编程，并与多个现有 webhook 发布服务器兼容。 

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

借助 Azure 事件网格，可以控制授予不同用户用来执行各种管理操作的访问级别，例如列出事件订阅、创建新的事件订阅及生成密钥。 事件网格会使用 Azure 基于角色的访问检查 (RBAC)。

### <a name="operation-types"></a>操作类型

Azure 事件网格支持下列操作：

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

最后三个操作可能会返回从常规读取操作中筛选出的机密信息。 最好限制这些操作的访问权限。 可以使用 [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)、[Azure 命令行接口](../role-based-access-control/role-assignments-cli.md) (CLI) 和 [REST API](../role-based-access-control/role-assignments-rest.md) 创建自定义角色。

### <a name="enforcing-role-based-access-check-rbac"></a>强制实施基于角色的访问检查 (RBAC)

使用以下步骤为不同的用户强制实施 RBAC：

#### <a name="create-a-custom-role-definition-file-json"></a>创建自定义角色定义文件 (.json)

以下是示例事件网格角色定义，可以让用户执行不同的操作集。

EventGridReadOnlyRole.json：仅允许只读操作。

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

EventGridNoDeleteListKeysRole.json：允许受限制的发布操作但禁止删除操作。

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

EventGridContributorRole.json：允许所有事件网格操作。

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

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>使用 Azure CLI 创建和分配自定义角色

若要创建自定义的角色，请使用：

```azurecli
az role definition create --role-definition @<file path>
```

若要向用户分配角色，请使用：

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)
