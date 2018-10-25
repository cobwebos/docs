---
title: 参数化 Azure 数据工厂中的链接服务 | Microsoft Docs
description: 了解如何参数化 Azure 数据工厂中的链接服务，并在运行时传递动态值。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 287dcdedede5cab575aa0b9a73ec3e122556dc93
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900717"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>参数化 Azure 数据工厂中的链接服务

现在可以参数化链接服务并在运行时传递动态值。 例如，如果要连接到同一 Azure SQL 数据库服务器上的不同数据库，则现在可以在链接服务定义中参数化数据库名称。 这可以避免必须为 Azure SQL 数据库服务器上的每个数据库创建链接服务。 也可以参数化链接服务定义中的其他属性 - 例如，用户名。

可以使用 Azure 门户中的数据工厂 UI 或编程接口来参数化链接服务。

> [!TIP]
> 我们建议不要参数化密码或机密。 而应将所有连接字符串都存储在 Azure Key Vault 中，并参数化*机密名称*。

## <a name="supported-data-stores"></a>支持的数据存储

目前，Azure 门户中的数据工厂 UI 支持以下数据存储的链接服务参数化。 对于所有其他数据存储，可以通过选择管道选项卡上的**代码**图标并使用 JSON 编辑器来参数化链接服务。
- Azure SQL 数据库
- Azure SQL 数据仓库
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

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
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
