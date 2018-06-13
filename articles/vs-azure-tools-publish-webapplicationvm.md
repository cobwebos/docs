---
title: Publish-WebApplicationVM | Microsoft Docs
description: 了解如何将 Web 应用程序部署到虚拟机。 此脚本会在 Azure 订阅中创建所需的资源（如果这些资源不存在）。
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 3fdd11387096d95359fb5f578ca64720f2182c45
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795905"
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publish-WebApplicationVM（Windows PowerShell 脚本）
将 Web 应用程序部署到虚拟机。 此脚本会在 Azure 订阅中创建所需的资源（如果这些资源不存在）。

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuration
描述部署详细信息的 JSON 配置文件的路径。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

### <a name="subscriptionname"></a>SubscriptionName
要在其中创建虚拟机的 Azure 订阅的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |使用订阅文件中的第一个订阅 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

### <a name="webdeploypackage"></a>WebDeployPackage
要发布到虚拟机的 Web 部署包的路径。 可以在 Visual Studio 中使用“发布 Web”向导来创建此包。 请参阅[如何：在 Visual Studio 中创建 Web 部署包](https://msdn.microsoft.com/library/dd465323.aspx)。

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

### <a name="allowuntrusted"></a>AllowUntrusted
如果为 true，则允许使用未被受信任的根证书颁发机构签署的证书。

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |false |
| 接受管道输入？ |false |
| 接受通配符？ |false |

### <a name="vmpassword"></a>VMPassword
虚拟机帐户的凭据。 示例：-VMPassword @{Name = "admin"; Password = "password"}

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |否 |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Azure 中的 SQL 数据库的凭据。 示例：-DatabaseServerPassword @{Name = "admin"; Password = "password"}

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
如果为 true，则将来自脚本的消息打印到输出流。

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置 |指定 |
| 默认值 |false |
| 接受管道输入？ |false |
| 接受通配符？ |false |

## <a name="remarks"></a>备注
有关如何使用脚本创建开发和测试环境的完整说明，请参阅[使用 Windows PowerShell 脚本发布到开发和测试环境](vs-azure-tools-publishing-using-powershell-scripts.md)。

JSON 配置文件指定要部署的内容的详细信息。 它包括创建项目时指定的信息，如名称、地缘组、VHD 映像和虚拟机的大小。 它还包括虚拟机上的终结点、要预配的数据库（如果有的话）和 Web 部署参数。 以下代码显示一个示例 JSON 配置文件：

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

可以编辑 JSON 配置文件以更改预配的内容。 虚拟机和云服务是必需的，但数据库部分则是可选的。

