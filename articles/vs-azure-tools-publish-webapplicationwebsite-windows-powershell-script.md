---
title: Publish-WebApplicationWebSite（Windows PowerShell 脚本）| Microsoft Docs
description: 了解如何将 Web 项目发布到 Azure 网站。 此脚本会在 Azure 订阅中创建所需的资源（如果这些资源不存在）。
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: b540734f17ed11e4c438e1248ed9612fb892e89a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publish-WebApplicationWebSite（Windows PowerShell 脚本）
## <a name="syntax"></a>语法
将 Web 项目发布到 Azure 网站。 此脚本会在 Azure 订阅中创建所需的资源（如果这些资源不存在）。

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuration
描述部署详细信息的 JSON 配置文件的路径。

| 参数 | 默认值 |
| --- | --- |
| 别名 |无 |
| 必需？ |是 |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

## <a name="subscriptionname"></a>SubscriptionName
要在其中创建网站的 Azure 订阅的名称。

| 参数 | 默认值 |
| --- | --- |
| 别名 |无 |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

## <a name="webdeploypackage"></a>WebDeployPackage
要发布到网站的 Web 部署包的路径。 可以在 Visual Studio 中使用“发布 Web”向导来创建此包。 有关详细信息，请参阅 [Azure 云服务和 ASP.NET 入门](http://go.microsoft.com/fwlink/p/?LinkID=623089)。

| 参数 | 默认值 |
| --- | --- |
| 别名 |无 |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |否 |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Azure 中的 SQL 数据库的用户名和密码。

| 参数 | 默认值 |
| --- | --- |
| 别名 |无 |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
如果为 true，则将来自脚本的消息打印到输出流。

| 参数 | 默认值 |
| --- | --- |
| 别名 |无 |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |false |
| 接受管道输入？ |false |
| 接受通配符？ |false |

## <a name="remarks"></a>备注
有关如何使用脚本创建开发和测试环境的完整说明，请参阅[使用 Windows PowerShell 脚本发布到开发和测试环境](vs-azure-tools-publishing-using-powershell-scripts.md)。

JSON 配置文件指定要部署的内容的详细信息。 它包括当创建项目时指定的信息，如网站的名称和用户名。 它还包括要预配的数据库（如果有的话）。 以下代码显示一个示例 JSON 配置文件：

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

可以编辑 JSON 配置文件以更改部署的内容。 网站部分是必需的，但数据库部分则是可选的。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅[Publish-WebApplicationVM（Windows PowerShell 脚本）](vs-azure-tools-publish-webapplicationvm.md)

