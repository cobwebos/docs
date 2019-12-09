---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: c505909599b6b69719de1cb9224db52d2f524b2b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935844"
---
## <a name="local-settings-file"></a>本地设置文件

本地. json 文件存储本地开发工具使用的应用设置、连接字符串和设置。 仅当在本地运行项目时，才使用本地. json 文件中的设置。 本地设置文件具有以下结构：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

在本地运行项目时，支持这些设置：

| 设置      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 如果此设置设置为 "`true`"，则所有值都使用本地计算机密钥进行加密。 与 `func settings` 命令配合使用。 默认值为 `false`。 |
| **`Values`** | 在本地运行项目时使用的应用程序设置和连接字符串的数组。 这些键值（字符串）对对应于 Azure 中函数应用的应用程序设置，如[`AzureWebJobsStorage`]。 许多触发器和绑定都有一个属性，该属性引用连接字符串应用设置，例如[Blob 存储触发器](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)`Connection`。 对于这些属性，需要在 `Values` 数组中定义一个应用程序设置。 <br/>对于除 HTTP 以外的触发器， [`AzureWebJobsStorage`]是必需的应用设置。 <br/>版本2.x 和更高版本的函数运行时需要 [`FUNCTIONS_WORKER_RUNTIME`] 设置，该设置是由核心工具为你的项目生成的。 <br/> 如果已在本地安装[Azure 存储模拟器](../articles/storage/common/storage-use-emulator.md)，并将[`AzureWebJobsStorage`]设置为 `UseDevelopmentStorage=true`，则核心工具使用仿真程序。 在开发过程中，模拟器非常有用，但在部署之前应使用实际的存储连接进行测试。<br/> 值必须是字符串，而不能是 JSON 对象或数组。 设置名称不能包含冒号（`:`）或双下划线（`__`）。 这些字符由运行时保留。  |
| **`Host`** | 在本地运行项目时，此节中的设置自定义函数主机进程。 这些设置与 host json 设置不同，后者也适用于在 Azure 中运行项目的情况。 |
| **`LocalHttpPort`** | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port` 命令行选项优先于此设置。 |
| **`CORS`** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (\*)，它允许使用任何来源的请求。 |
| **`CORSCredentials`** |  如果设置为 `true`，则允许 `withCredentials` 请求。 |
| **`ConnectionStrings`** | 一个集合。 请勿将此集合用于函数绑定所使用的连接字符串。 此集合仅用于通常从配置文件的 `ConnectionStrings` 节获取连接字符串的框架，如[实体框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 此对象中的连接字符串添加到提供者类型为 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 的环境中。 此集合中的项目未与其他应用设置一起发布到 Azure。 必须将这些值显式添加到 function app 设置的 `Connection strings` 集合。 如果要在函数代码中创建[`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) ，则应在门户中的 "**应用程序设置**" 中将连接字符串值与其他连接存储。 |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
