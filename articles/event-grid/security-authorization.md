---
title: Azure 事件网格安全和身份验证
description: 介绍 Azure 事件网格及其概念。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 5f8b0a779e6cb70537d126c251e1e065892934a9
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629501"
---
# <a name="authorizing-access-to-event-grid-resources"></a>授权访问事件网格资源
借助 Azure 事件网格，可以控制授予不同用户用来执行各种管理操作的访问级别，例如列出事件订阅、创建新的事件订阅及生成密钥。 事件网格使用 Azure 的基于角色的访问控制 (RBAC)。


## <a name="operation-types"></a>操作类型

事件网格支持下列操作：

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

最后三个操作可能会返回从常规读取操作中筛选出的机密信息。 建议限制对这些操作的访问。 

## <a name="built-in-roles"></a>内置角色

事件网格提供了用于管理事件订阅的两个内置角色。 它们对于实现[事件域](event-domains.md)很重要，因为它们向用户授予了订阅事件域中的主题所需的权限。 这些角色专注于事件订阅，不授予对创建主题等操作的访问权限。

你可以[将这些角色分配给用户或组](../role-based-access-control/quickstart-assign-role-user-portal.md)。

**EventGrid EventSubscription 参与者**：管理事件网格订阅操作

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
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

**EventGrid EventSubscription 读者**：读取事件网格订阅

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
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

## <a name="custom-roles"></a>自定义角色

如果需要指定不同于内置角色的权限，可以创建自定义角色。

下面是允许用户采取不同操作的示例事件网格角色定义。 这些自定义角色与内置角色不同，因为它们授予比只是事件订阅更广泛的访问权限。

EventGridReadOnlyRole.json****：仅允许只读操作。

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

EventGridNoDeleteListKeysRole.json****：允许受限制的发布操作但禁止删除操作。

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

EventGridContributorRole.json****：允许所有事件网格操作。

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



### <a name="encryption-at-rest"></a>静态加密

事件网格服务写入到磁盘的所有事件或数据都通过 Microsoft 托管的密钥进行加密，确保静态加密。 此外，事件或数据保留的最长时间为24小时，并遵循[事件网格重试策略](delivery-and-retry.md)。 事件网格将在 24 小时或事件生存时间（以两者中较小者为准）过后自动删除所有事件或数据。

## <a name="permissions-for-event-subscriptions"></a>事件订阅的权限
如果你使用不是 WebHook 的事件处理程序（例如事件中心或队列存储），则需要对该资源具有写入访问权限。 此权限检查可防止未经授权的用户向你的资源发送事件。

你必须在作为事件源的资源上具有 **Microsoft.EventGrid/EventSubscriptions/Write** 权限。 因为要在资源范围内写入新的订阅，所以需要此权限。 所需资源因是订阅系统主题还是订阅自定义主题而异。 本部分介绍了这两种类型。

### <a name="system-topics-azure-service-publishers"></a>系统主题（Azure 服务发布服务器）
对于系统主题，需要在资源范围内写入新事件订阅的权限，才能发布该事件。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

例如，若要订阅存储帐户上名为“myacct”的事件，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限****

### <a name="custom-topics"></a>自定义主题
对于自定义主题，需要在事件网格主题范围内写入新事件订阅的权限。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

例如，若要订阅名为“mytopic”的自定义主题，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限****



## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)
