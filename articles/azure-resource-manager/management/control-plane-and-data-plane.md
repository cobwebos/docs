---
title: 控制平面和数据平面操作
description: 介绍控制平面操作和数据平面操作之间的差异。 控制平面操作由 Azure 资源管理器处理。 数据平面操作由服务处理。
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371263"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure 控制平面和数据平面

Azure 操作可以分为两个类别 - 控制平面和数据平面。 本文介绍这两种类型的操作之间的差异。

使用控制平面可管理订阅中的资源。 可以使用数据平面来使用由资源类型的实例公开的功能。

例如：

* 通过控制平面创建虚拟机。 创建虚拟机后，通过数据平面操作（如远程桌面协议 (RDP)）与该虚拟机进行交互。

* 通过控制平面创建存储帐户。 然后，使用数据平面在存储帐户中读取和写入数据。

* 通过控制平面创建 Cosmos 数据库。 为了查询 Cosmos 数据库中的数据，可以使用数据平面。

## <a name="control-plane"></a>控制面板

对控制平面操作的所有请求都将发送到 Azure 资源管理器 URL。 该 URL 因 Azure 环境而异。

* 对于 Azure 全球，该 URL 是 `https://management.azure.com`。
* 对于 Azure 政府，该 URL 是 `https://management.usgovcloudapi.net/`。
* 对于 Azure 德国，该 URL 是 `https://management.microsoftazure.de/`。
* 对于 Microsoft Azure 中国世纪互联，该 URL 是 `https://management.chinacloudapi.cn`。

若要发现哪些操作使用 Azure 资源管理器 URL，请参阅 [Azure REST API](/rest/api/azure/)。 例如，用于 MySql 的[创建或更新操作](/rest/api/mysql/databases/createorupdate)是控制平面操作，因为请求 URL 是：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure 资源管理器处理所有控制平面请求。 它将自动应用已实现的 Azure 功能来管理资源，例如：

* [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [管理锁](lock-resources.md)
* [活动日志](view-activity-logs.md)

对请求进行身份验证后，Azure 资源管理器会将其发送到资源提供程序，该资源提供程序会完成操作。

控制平面包括两个用于处理请求的方案 -“绿色字段”和“棕色字段”。 “绿色字段”指的是新资源。 “棕色字段”指的是现有资源。 部署资源时，Azure 资源管理器会了解何时要创建新资源以及何时要更新现有资源。 你不必担心相同的资源会被创建。

## <a name="data-plane"></a>数据平面

对数据平面操作的请求将发送到特定于你的实例的终结点。 例如，认知服务中的[检测语言操作](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language)是一项数据平面操作，因为请求 URL 是：

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

数据平面操作不限于 REST API。 它们可能需要其他凭据，例如在登录到虚拟机或数据库服务器时。

那些强制执行管理和调控的功能可能不适用于数据平面操作。 你需要考虑用户与你的解决方案交互的不同方式。 例如，某个阻止用户删除数据库的锁不会阻止用户通过查询删除数据。

你可以使用某些策略来调控数据平面操作。 有关详细信息，请参阅 [Azure Policy 中的资源提供程序模式（预览版）](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的概述，请参阅[什么是 Azure 资源管理器？](overview.md)

* 若要详细了解策略定义对新资源和现有资源的影响，请参阅[评估新 Azure Policy 定义的影响](../../governance/policy/concepts/evaluate-impact.md)。
