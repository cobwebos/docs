---
title: "Azure 政府版数据库 | Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Azure 政府版数据库
## <a name="sql-database"></a>SQL 数据库
有关元数据可见性配置和保护最佳实践的其他指导，请参阅 <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">Microsoft 安全中心的 SQL 数据库引擎</a>和 [Azure SQL Database 公共文档](../sql-database/index.md)。

### <a name="variations"></a>变体
SQL V12 数据库已在 Azure Government 中正式发布。

Azure Government 中的 SQL Azure 服务器地址不相同：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| SQL 数据库 |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>注意事项
以下信息标识 Azure SQL 的 Azure 政府版边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| Microsoft Azure SQL 中存储和处理的所有数据均可包含 Azure Government 管理的数据。 你必须使用数据库工具进行 Azure Government 所管理数据的数据传输。 |Azure SQL 元数据不允许包含导出控制数据。 此元数据包括创建和维护存储产品时输入的所有配置数据。  不要将管理/控制数据输入到以下字段：数据库名称、订阅名称、资源组、服务器名称、服务器管理员登录、部署名称、资源标记 |

## <a name="azure-redis-cache"></a>Azure Redis Cache
有关此服务以及如何使用此服务的详细信息，请参阅 [Azure Redis 缓存公共文档](../redis-cache/index.md)。

### <a name="variations"></a>变体
在 Azure 政府版中，用于访问和管理 Azure Redis 缓存的 URL 不同：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| 缓存终结点 |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Azure 门户 |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> 所有脚本和代码均需要说明相应终结点和环境。 有关详细信息，请参阅[如何连接到 Azure 政府版云](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)。
> 
> 

### <a name="considerations"></a>注意事项
以下信息标识 Azure Redis 缓存的 Azure 政府版边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| Azure Redis 缓存中存储和处理的所有数据均可包含 Azure 政府版管理的数据。 |Azure Redis 缓存元数据不允许包含导出控制数据。 不要在以下字段中输入受管理/控制的数据：缓存名称、VNET 名称、订阅名称、资源组、资源标记、Redis 属性。 |

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 博客</a>。




<!--HONumber=Nov16_HO3-->


