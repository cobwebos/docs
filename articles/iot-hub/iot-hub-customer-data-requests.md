---
title: 客户数据请求功能
description: 客户数据请求功能摘要
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 73da48d449a7cc5cdca598c8aef176952909ed85
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634917"
---
# <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要

Azure IoT 中心是一项基于 REST API 的云服务，其目标是企业客户，可以在数百万台设备和分区的 Azure 服务之间进行安全的双向通信。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

单台设备由租户管理员分配一个设备标识符（设备 ID）。 设备数据基于分配的设备 ID。 Microsoft 不保留任何信息，也无法访问将设备 ID 与用户相关联的数据。

在 Azure IoT 中心托管的许多设备（例如办公室调温器或工厂机器人）不是个人设备。 不过，客户可以将某些设备视为可以进行个人标识的设备，并可自行保留将设备与个人绑定的个人资产或库存跟踪方法。 Azure IoT 中心将所有与设备关联的数据视为个人数据来管理和存储。

租户管理员可以使用 Azure 门户或服务的 REST API 来处理信息请求，只需导出或删除与设备 ID 相关联的数据即可。

如果使用 Azure IoT 中心服务的路由功能将设备消息转发给其他服务，则若要完成给定设备的完整请求，必须由每个路由终结点的租户管理员执行数据请求。 如需更多详细信息，请参阅每个终结点的参考文档。 有关支持的终结点的详细信息，请参阅[参考 - IoT 中心终结点](iot-hub-devguide-endpoints.md)。

如果使用 Azure IoT 中心服务的 Azure 事件网格集成功能，则必须由这些事件的每个订阅服务器的租户管理员执行数据请求操作。 有关详细信息，请参阅[使用事件网格响应 IoT 中心事件](iot-hub-event-grid.md)。

如果使用 Azure IoT 中心服务的 Azure Monitor 集成功能来创建诊断日志，则必须由租户管理员针对存储的日志执行数据请求操作。 有关详细信息，请参阅[监视 Azure IoT 中心的运行状况](iot-hub-monitor-resource-health.md)。

## <a name="deleting-customer-data"></a>删除客户数据

租户管理员可以在 Azure 门户中使用 Azure IoT 中心扩展的 IoT 设备边栏选项卡来删除设备，而删除设备同时也会删除与该设备关联的数据。

也可使用 REST API 对设备执行删除操作。 有关详细信息，请参阅 [Device Api - Delete Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice)（设备 API - 删除设备）。

## <a name="exporting-customer-data"></a>导出客户数据

在 Azure 门户中，租户管理员可以利用 Azure IoT 中心扩展的 IoT 设备边栏选项卡中的复制和粘贴功能来导出与设备关联的数据。

也可使用 REST API 对设备执行导出操作。 有关详细信息，请参阅 [Device Api - Get Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice)（设备 API - 获取设备）。

> [!NOTE]
> 当你使用 Microsoft 的企业服务时，Microsoft 会生成一些信息，称为系统生成的日志。 租户管理员无法访问或导出 Azure IoT 中心的某些系统生成日志。 这些日志包含在服务中执行的实际操作以及与个人设备相关的诊断数据。

## <a name="links-to-additional-documentation"></a>其他文档的链接

Azure IoT 中心设备 API 的完整文档位于 [https://docs.microsoft.com/rest/api/iothub/deviceapi](https://docs.microsoft.com/rest/api/iothub/deviceapi)。