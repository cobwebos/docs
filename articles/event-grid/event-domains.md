---
title: Azure 事件网格中的事件域
description: 介绍如何使用事件域来管理 Azure 事件网格中的主题。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: ef0a9213d095d0b7ae4343e2af145236a7e005a1
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305408"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>了解用于管理事件网格主题的事件域

本文介绍如何使用事件域来管理各种业务组织、客户或应用程序的自定义事件流。 事件域可用于：

* 管理大规模的多租户事件处理体系结构。
* 管理授权和身份验证。
* 对主题进行分区，而不单独管理每个主题。
* 避免单独发布到每个主题终结点。

## <a name="event-domain-overview"></a>事件域概述

事件域是用于管理有关同一应用程序的大量事件网格主题的工具。 可将其视为可包含数千个单独主题的元主题。

事件域提供 Azure 服务（例如存储和 IoT 中心）使用的相同体系结构来发布其事件。 它们可以将事件发布到数千个主题。 域还提供对每个主题的授权和身份验证控制，以便你可对租户进行分区。

### <a name="example-use-case"></a>示例用例

解释事件域最简单的方法是采用示例。 假设你经营着 Contoso 建筑机械公司，公司制造拖拉机、挖掘设备和其他重型机械。 运营业务时，可向客户推送有关设备维护、系统运行状况、合同更新的实时信息。 所有这些信息都会发布到各种终结点，包括应用、客户终结点以及客户已安装的其他基础结构。

事件域允许将 Contoso 建筑机械公司建模为单个事件实体。 每个客户表示为域中的主题。 使用 Azure Active Directory 处理身份验证和授权。 每个客户都可订阅他们的主题并获取传送的事件。 通过事件域管理访问权限可确保客户仅能访问其主题。

此外，你还可获得一个单个终结点，可将所有客户事件发布至此终结点。 事件网格将负责确保每个主题仅获取其租户范围内的事件。

![Contoso 建筑公司示例](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>访问管理

使用域，可通过 Azure 的基于角色的访问控制 (RBAC) 精细控制每个主题的授权和身份验证情况。 这些角色可用于将应用程序中的每个租户限制为仅可访问经授权的主题。

事件域中 RBAC 的工作方式与[托管访问控制](security-authentication.md#management-access-control)在事件网格和 Azure 的其余部分中的工作方式相同。 使用 RBAC 在事件域中创建和强制实施自定义角色定义。

### <a name="built-in-roles"></a>内置角色

事件网格提供两个内置角色定义，使 RBAC 可更方便地用于事件域。 这些角色是“EventGrid EventSubscription 参与者（预览版）”和“EventGrid EventSubscription 读取者（预览版）”。   将这些角色分配到需要订阅事件域中的主题的用户。 将角色分配的范围限定为用户需要订阅的主题。

有关这些角色的信息，请参阅[事件网格的内置角色](security-authentication.md#built-in-roles)。

## <a name="subscribing-to-topics"></a>订阅主题

订阅事件域中主题的事件与[创建自定义主题的事件订阅](./custom-event-quickstart.md)或订阅 Azure 服务中的事件相同。

### <a name="domain-scope-subscriptions"></a>域范围订阅

事件域还允许域范围订阅。 在事件域上订阅事件后，无论事件发送到哪个主题，都会收到发送到域的所有事件。 域范围订阅可用于管理和审核工作。

## <a name="publishing-to-an-event-domain"></a>发布到事件域

创建事件域时，将获得一个发布终结点，这与在事件网格中创建主题时类似。 

若要将事件发布到事件域中的主题，请将事件推送到域的终结点，[方法与使用自定义主题时相同](./post-to-custom-topic.md)。 唯一的区别是，必须指定要将事件传递到的主题。

例如，发布以下事件数组后，会将带有 `"id": "1111"` 的事件发送到主题 `foo`，带有 `"id": "2222"` 的事件发送到主题 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

事件域会自动处理发布到主题的工作。 可将所有事件发布到域的终结点，而无需将事件发布到单独管理的每个主题。 事件网格确保将每个事件发送到正确的主题。

## <a name="limits-and-quotas"></a>限制和配额
下面是与事件域相关的限制和配额:

- 100000每个事件域的主题 
- 每个 Azure 订阅100个事件域 
- 事件域中的每个主题 500 个事件订阅
- 50域范围订阅 
- 每秒5000事件引入速率 (域中)

如果这些限制无法满足你的需要, 请通过打开支持票证或向发送电子邮件来[askgrid@microsoft.com](mailto:askgrid@microsoft.com)联系产品团队。 

## <a name="pricing"></a>定价
事件域使用的操作定价与事件网格中的所有其他功能使用的[操作定价](https://azure.microsoft.com/pricing/details/event-grid/)相同。

操作在事件域中的工作方式与在自定义主题中的相同。 事件域每引入一个事件都为一个操作，每尝试传递一个事件都为一个操作。

## <a name="next-steps"></a>后续步骤

* 若要了解有关设置事件域、创建主题、创建事件订阅和发布事件的信息，请参阅[管理事件域](./how-to-event-domains.md)。
