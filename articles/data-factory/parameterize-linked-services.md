---
title: 参数化 Azure 数据工厂中的链接服务
description: 了解如何参数化 Azure 数据工厂中的链接服务，并在运行时传递动态值。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 85689661e7f0d170cd88edde8985f46285e679c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987785"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>参数化 Azure 数据工厂中的链接服务
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

现在可以参数化链接服务并在运行时传递动态值。 例如，如果要连接到同一逻辑 SQL Server 上的不同数据库，则现在可以在链接服务定义中参数化数据库名称。 这样就不需为逻辑 SQL Server 上的每个数据库创建链接服务。 也可以参数化链接服务定义中的其他属性 - 例如，用户名。

可以使用 Azure 门户中的数据工厂 UI 或编程接口来参数化链接服务。

> [!TIP]
> 我们建议不要参数化密码或机密。 而应将所有连接字符串都存储在 Azure Key Vault 中，并参数化*机密名称*。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>支持的数据存储

目前，以下数据存储的数据工厂 UI 支持链接服务参数化。 对于所有其他数据存储，可以通过选择“连接”选项卡上的**代码**图标并使用 JSON 编辑器来参数化链接的服务。

- Amazon Redshift
- Azure Cosmos DB （SQL API）
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics（以前称为 SQL DW）
- MySQL
- Oracle
- SQL Server

## <a name="data-factory-ui"></a>数据工厂 UI

![将动态内容添加到“链接服务”定义](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![新建参数](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
