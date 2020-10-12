---
title: Azure 事件网格中的自定义主题
description: 介绍 Azure 事件网格中的自定义主题。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113779"
---
# <a name="custom-topics-in-azure-event-grid"></a>Azure 事件网格中的自定义主题
事件网格主题提供一个可供源在其中发送事件的终结点。 发布服务器创建事件网格主题，并决定事件源需要一个主题还是多个主题。 主题用于收集相关事件。 为了对某些类型的事件作出响应，订阅服务器将决定要订阅的主题。

 自定义主题是应用程序和第三方的主题。 在创建或分配了对自定义主题的访问权限后，就会在订阅中看到该自定义主题。 

设计应用程序时，可以灵活地决定要创建多少个主题。 对于大型解决方案，请为每个类别的相关事件创建自定义主题   。 例如，假设有一个应用程序发送与修改用户帐户和处理订单相关的事件。 任何事件处理程序都不太可能同时需要这两个类别的事件。 请创建两个自定义主题，让事件处理程序订阅其感兴趣的主题。 对于小型解决方案，可能希望将所有事件发送到单个主题。 事件订阅者可以筛选他们所需的事件类型。

## <a name="event-schema"></a>事件架构
有关事件架构的详细概述，请参阅 [Azure 事件网格事件架构](event-schema.md)。 对于自定义主题，事件发布者确定数据对象  。 顶级数据应具有与标准资源所定义事件相同的字段。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

以下部分提供了一些指向教程的链接，这些教程分别介绍如何使用 Azure 门户、CLI、PowerShell 和 Azure 资源管理器 (ARM) 模板来创建自定义主题。 


## <a name="azure-portal-tutorials"></a>Azure 门户教程
|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure 门户创建和路由自定义事件](custom-event-quickstart-portal.md) | 说明如何使用门户发送自定义事件。 |
| [快速入门：将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |
| [如何：发布到自定义主题](post-to-custom-topic.md) | 说明如何将事件发布到自定义主题。 |


## <a name="azure-cli-tutorials"></a>Azure CLI 教程
|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure CLI 创建和路由自定义事件](custom-event-quickstart.md) | 说明如何使用 Azure CLI 发送自定义事件。 |
| [Azure CLI：创建事件网格自定义主题](./scripts/event-grid-cli-create-custom-topic.md)|用于创建自定义主题的示例脚本。 该脚本检索终结点和密钥。|
| [Azure CLI：订阅自定义主题的事件](./scripts/event-grid-cli-subscribe-custom-topic.md)|用于创建自定义主题的订阅的示例脚本。 它将事件发送到 WebHook。|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell 教程
|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure PowerShell 创建和路由自定义事件](custom-event-quickstart-powershell.md) | 说明如何使用 Azure PowerShell 发送自定义事件。 |
| [PowerShell：创建事件网格自定义主题](./scripts/event-grid-powershell-create-custom-topic.md)|用于创建自定义主题的示例脚本。 该脚本检索终结点和密钥。|
| [PowerShell：订阅自定义主题的事件](./scripts/event-grid-powershell-subscribe-custom-topic.md)|用于创建自定义主题的订阅的示例脚本。 它将事件发送到 WebHook。|

## <a name="arm-template-tutorials"></a>ARM 模板教程
|标题  |说明  |
|---------|---------|
| [资源管理器模板：自定义主题和 WebHook 终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | 用于创建自定义主题及其订阅的资源管理器模板。 它将事件发送到 WebHook。 |
| [资源管理器模板：自定义主题和事件中心终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 用于创建自定义主题的订阅的资源管理器模板。 它将事件发送到 Azure 事件中心。 |

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [系统主题](system-topics.md)
- [域](event-domains.md)