---
title: "Azure 事件网格安全和身份验证"
description: "介绍 Azure 事件网格及其概念。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: e2f48b6e72072ce6bf019b3adc138ae83c162f25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-security-and-authentication"></a>事件网格安全和身份验证 

Azure 事件网格包含三种类型的身份验证：

* 事件订阅
* 事件发布
* WebHook 事件传送

## <a name="webhook-event-delivery"></a>WebHook 事件传送

Webhook 是从 Azure 事件网格实时接收事件的多种方式之一。 每当新事件准备好进行传送时，事件网格 WebHook 会向已配置的 HTTP 终结点发送 HTTP 请求，并在正文中包含该事件。

当你向事件网格注册自己的 WebHook 终结点时，事件网格为证明终结点所有权，会向你发送 POST 请求，其中包含简单的验证代码。 应用需要通过回显验证代码，做出响应。 事件网格不会将事件传送到未通过验证的 WebHook 终结点。

### <a name="validation-details"></a>验证详细信息

* 在创建/更新事件订阅时，事件网格会将“SubscriptionValidationEvent”事件发送到目标终结点。
* 该事件包含标头值“Aeg-Event-Type: SubscriptionValidation”。
* 事件正文具有与其他事件网格事件相同的架构。
* 事件数据包括“validationCode”属性，其中含有随机生成的字符串。 例如，“validationCode: acb13…”。

以下示例显示了 SubscriptionValidationEvent 示例：

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}]
```

为证明终结点所有权，请在 validationResponse 属性中回显 验证代码，如下例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

最后，请务必注意 Azure 事件网格仅支持 HTTPS webhook 终结点。

## <a name="event-subscription"></a>事件订阅

若要订阅事件，必须具有所需资源的 Microsoft.EventGrid/EventSubscriptions/Write 权限。 因为要在资源范围内写入新的订阅，所以需要此权限。 所需资源因订阅系统主题或自定义主题而异。 本部分介绍了这两种类型。

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

资源是将事件发送到的主题的路径。 例如，有效的资源路径是：`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

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

借助 Azure 事件网格，可以控制授予不同用户用来执行各种管理操作的访问级别，例如列出事件订阅、创建新的事件订阅及生成密钥。 事件网格会利用 Azure 基于角色的访问检查 (RBAC)。

### <a name="operation-types"></a>操作类型

Azure 事件网格支持下列操作：

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

最后三个操作可能会返回从常规读取操作中筛选出的机密信息。 最好限制这些操作的访问权限。 可以使用 [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)、[Azure 命令行接口](../active-directory/role-based-access-control-manage-access-azure-cli.md) (CLI) 和 [REST API](../active-directory/role-based-access-control-manage-access-rest.md) 创建自定义角色。

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
