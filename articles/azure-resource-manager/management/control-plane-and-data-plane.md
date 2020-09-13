---
title: 控制面和数据平面操作
description: 描述控制平面和数据平面操作之间的差异。 控制平面操作由 Azure 资源管理器处理。 数据平面操作由服务处理。
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: a0575c37c80417d9859ef36366dc9f26cdb4dbe4
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90038975"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure 控制面和数据平面

Azure 操作可以分为两个类别-控制平面和数据平面。 本文介绍这两种类型的操作之间的差异。

使用控制平面管理订阅中的资源。 您可以使用数据平面来使用由您的资源类型的实例公开的功能。

例如：

* 通过控制平面创建虚拟机。 创建虚拟机后，可以通过数据平面操作（如远程桌面协议 (RDP) ）与该虚拟机进行交互。

* 通过控制平面创建存储帐户。 使用数据平面可以在存储帐户中读取和写入数据。

* 通过控制平面创建 Cosmos 数据库。 若要查询 Cosmos 数据库中的数据，请使用数据平面。

## <a name="control-plane"></a>控制面板

控制平面操作的所有请求都将发送到 Azure 资源管理器 URL。 该 URL 因 Azure 环境而异。

* 对于 Azure global，URL 为 `https://management.azure.com` 。
* 对于 Azure 政府版，URL 为 `https://management.usgovcloudapi.net/` 。
* 对于 Azure 德国，URL 是 `https://management.microsoftazure.de/` 。
* 对于 Microsoft Azure 中国世纪互联，URL 为 `https://management.chinacloudapi.cn` 。

若要发现哪些操作使用 Azure 资源管理器 URL，请参阅 [azure REST API](/rest/api/azure/)。 例如，MySql 的 [create 或 update 操作](/rest/api/mysql/databases/createorupdate) 是一个控制平面操作，因为请求 URL 是：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure 资源管理器处理所有控制平面请求。 它将自动应用已实现的 Azure 功能来管理资源，例如：

* [Azure 基于角色的访问控制 (RBAC) ](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [管理锁](lock-resources.md)
* [活动日志](view-activity-logs.md)

对请求进行身份验证后，Azure 资源管理器会将其发送到资源提供程序，这会完成操作。

控制平面包括两个用于处理请求的方案-"绿色字段" 和 "棕色字段"。 绿色字段引用新资源。 棕色字段引用现有资源。 部署资源时，Azure 资源管理器了解何时创建新资源以及何时更新现有资源。 不必担心会创建相同的资源。

## <a name="data-plane"></a>数据平面

数据平面操作的请求将发送到特定于你的实例的终结点。 例如，认知服务中的 " [检测语言" 操作](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) 是一种数据平面操作，因为请求 URL 是：

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

数据平面操作不限于 REST API。 它们可能需要其他凭据，例如登录到虚拟机或数据库服务器。

强制执行管理和调控的功能可能不适用于数据平面操作。 您需要考虑用户与您的解决方案交互的不同方式。 例如，阻止用户删除数据库的锁不会阻止用户通过查询删除数据。

您可以使用某些策略来控制数据平面操作。 有关详细信息，请参阅 [Azure 策略中 (预览) 资源提供程序模式](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的概述，请参阅 [什么是 azure 资源管理器？](overview.md)

* 若要了解有关策略定义对新资源和现有资源的影响的详细信息，请参阅 [评估新的 Azure 策略定义的影响](../../governance/policy/concepts/evaluate-impact.md)。
