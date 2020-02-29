---
title: 暂停、恢复、缩放 REST Api
description: 通过 REST Api 管理 Azure Synapse Analytics 数据仓库中的计算能力。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e3435fdaa505a73abf96b9463b061c623c192ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199387"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库的 REST API
用于管理 Azure Synapse Analytics 数据仓库中的计算的 REST Api。

## <a name="scale-compute"></a>缩放计算
若要更改数据仓库单位，请使用[创建或更新数据库](/rest/api/sql/databases/createorupdate) REST API。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的数据仓库单位设置为 DW1000。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>暂停计算

若要暂停数据库，请使用[暂停数据库](/rest/api/sql/databases/pause) REST API。 以下示例将暂停 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>恢复计算

若要启动数据库，请使用[恢复数据库](/rest/api/sql/databases/resume) REST API。 以下示例将启动 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>检查数据库状态

> [!NOTE]
> 当前检查数据库状态可能在数据库完成联机工作流时返回联机状态，从而导致连接错误。 如果使用此 API 调用来触发连接尝试，则可能需要在应用程序代码中添加2到3分钟的延迟。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>获取维护计划
检查已为数据仓库设置的维护计划。 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>设置维护计划
设置和更新现有数据仓库的维护计划。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。

