---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: aae89e1c6f8db2fb657ac2a43c4bce0396ab3ddd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377126"
---
## <a name="local-settings-file"></a>本地设置文件

local.settings.json 文件存储应用设置、连接字符串和本地开发工具使用的设置。 只有在本地运行项目时，才会使用 local.settings.json 文件中的设置。 本地设置文件的结构如下：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

| 设置      | 说明                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 当此设置设为 `true` 时，所有值都使用本地计算机密钥进行加密。 与 `func settings` 命令配合使用。 默认值为 `false`。 当本地计算机上的 local.settings.json 文件中包含机密（如服务连接字符串）时，可能需要对其进行加密。 主机在运行时会自动对设置解密。 在尝试读取本地加密设置之前，请使用 `func settings decrypt` 命令。 |
| **`Values`** | 在本地运行项目时使用的应用程序设置和连接字符串的数组。 这些键值 (string-string) 对与 Azure 的函数应用中的应用程序设置相对应，例如 [`AzureWebJobsStorage`]。 许多触发器和绑定都有一个引用连接字符串应用设置的属性，例如 [Blob 存储触发器](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)的 `Connection`。 对于此类属性，你需要一个在 `Values` 数组中定义的应用程序设置。 在下表中查看常用设置的列表。 <br/>值必须是字符串，而不能是 JSON 对象或数组。 设置名称不能包含冒号 (`:`) 或双下划线 (`__`)。 双下划线字符由运行时保留，并保留冒号以支持[依赖项注入](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings)。 |
| **`Host`** | 在本地运行项目时，本部分中的设置会自定义 Functions 主机进程。 这些设置独立于 host json 设置，后者在 Azure 中运行项目时也适用。 |
| **`LocalHttpPort`** | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port` 命令行选项优先于此设置。 |
| **`CORS`** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (\*)，它允许使用任何来源的请求。 |
| **`CORSCredentials`** |  设置为 `true` 时，允许 `withCredentials` 请求。 |
| **`ConnectionStrings`** | 一个集合。 不要将此集合用于函数绑定使用的连接字符串。 此集合仅供通常从配置文件的 `ConnectionStrings` 节获取连接字符串的框架使用，例如[实体框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 此对象中的连接字符串添加到提供者类型为 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 的环境中。 此集合中的项不会使用其他应用设置发布到 Azure。 必须将这些值显式添加到函数应用设置的 `Connection strings` 集合中。 如果要在函数代码中创建 [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)，则应将连接字符串值与其他连接一起存储在门户中的应用程序设置中。 |

在本地运行时，以下应用程序设置可包括在 `Values` 中：

| 设置 | 值 | 说明 |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| 存储帐户连接字符串，或者<br/>`UseDevelopmentStorage=true`| 包括 Azure 存储帐户的连接字符串。 如果使用 HTTP 之外的触发器，则是必需的。 有关详细信息，请查看 [`AzureWebJobsStorage`] 参考。<br/>在本地安装 [Azure 存储模拟器](../articles/storage/common/storage-use-emulator.md)后，可以将 [`AzureWebJobsStorage`] 设置为 `UseDevelopmentStorage=true`，以便 Core Tools 使用此模拟器。 此模拟器在开发期间非常有用，但是在部署之前，应当使用实际的存储连接进行测试。| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | 要在本地运行时禁用函数，请向集合添加 `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"`，其中 `<FUNCTION_NAME>` 是函数的名称。 要了解详细信息，请参阅[如何在 Azure Functions 中禁用函数](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| 指示 Functions 运行时的目标语言。 对于 Functions 运行时版本 2.x 及更高版本来说是必需的。 此设置是 Core Tools 为你的项目生成的。 要了解详细信息，请查看 [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) 参考。|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |指示在本地运行时使用 PowerShell 7。 如果未设置，则使用 PowerShell Core 6。 仅当在本地运行时才使用此设置。 在 Azure 中运行时，PowerShell 运行时版本由 `powerShellVersion` 站点配置设置决定，后者可[在门户中设置](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version)。 | 

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
