---
title: 如何面向 Azure Functions 运行时版本
description: Azure Functions 支持多个版本的运行时。 了解如何在 Azure 中指定函数应用的运行时版本。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926569"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 有三个主版本：[1.x、2.x 和 3.x](functions-versions.md)。 默认情况下，函数应用采用 3.x 版的运行时创建。 本文介绍如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

## <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新

Azure Functions 允许你通过使用函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置来针对特定版本的运行时。 函数应用将一直保留在指定的主版本上，直到显式选择迁移到新版本。 如果仅指定主版本，当运行时的新次要版本可用时，函数应用将自动更新到该版本。 新的次要版本不应引入中断性变更。 

如果指定次要版本（例如，“2.0.12345”），则函数应用将被固定到该特定版本，直到显式更改它。 旧的次要版本定期从生产环境中删除。 发生这种情况后，函数应用将在最新版本上运行，而不是在中设置的版本 `FUNCTIONS_EXTENSION_VERSION` 。 因此，你应该快速解决需要特定次要版本的函数应用的任何问题，以便可以改为针对主要版本。 [应用服务公告](https://github.com/Azure/app-service-announcements/issues)中公布了次版本的删除。

> [!NOTE]
> 如果你固定到 Azure Functions 的特定主要版本，然后尝试使用 Visual Studio 发布到 Azure，则会弹出一个对话框窗口，提示你将更新到最新版本或取消发布。 若要避免出现此情况，请在 `.csproj` 文件中添加 `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` 属性。

有新版本公开可用时，门户中将显示提示，让你可以升级到该版本。 升级至新版本后，始终可使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置移回到之前的版本。

下表显示了启用自动更新所需的每个主版本的 `FUNCTIONS_EXTENSION_VERSION` 值：

| 主版本 | `FUNCTIONS_EXTENSION_VERSION` 值 |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

更改运行时版本会导致函数应用重启。

## <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

可以更改函数应用使用的运行时版本。 由于可能会成为中断性变更，因此只能在函数应用中创建任何函数之前更改运行时版本。 

> [!IMPORTANT]
> 虽然运行时版本由 `FUNCTIONS_EXTENSION_VERSION` 设置决定，但你应该在 Azure 门户中进行此更改，而不是直接更改设置。 这是因为该门户会验证你的更改，并根据需要进行其他相关更改。

# <a name="portal"></a>[门户](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> 使用 Azure 门户，无法更改已包含函数的函数应用的运行时版本。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

还可以通过 Azure CLI 查看和更新 `FUNCTIONS_EXTENSION_VERSION`。  

使用 Azure CLI 时，可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令查看当前的运行时版本。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 

在以下输出中，你会看到 `FUNCTIONS_EXTENSION_VERSION`，为了清晰起见，该输出已被截断：

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令更新函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 设置。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

将 `<FUNCTION_APP>` 替换为你的函数应用的名称。 此外，还使用函数应用的资源组名称替代 `<RESOURCE_GROUP>`。 此外，将替换为 `<VERSION>` 特定版本、或 `~3` `~2` `~1` 。

可以通过在前面代码示例中选择“试一试”**** 运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az-login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要检查 Azure Functions 运行时，请使用以下 cmdlet： 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

将替换 `<FUNCTION_APP>` 为函数应用和的名称 `<RESOURCE_GROUP>` 。 `FUNCTIONS_EXTENSION_VERSION`在哈希表中返回该设置的当前值。

使用以下脚本更改函数运行时：

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

与之前一样， `<FUNCTION_APP>` 将替换为函数应用的名称，将替换为 `<RESOURCE_GROUP>` 资源组的名称。 此外，请 `<VERSION>` 将替换为特定版本或主要版本，如 `~2` 或 `~3` 。 您可以 `FUNCTIONS_EXTENSION_VERSION` 在返回的哈希表中验证设置的更新值。 

---

在对应用程序设置进行更改后，函数应用将重新启动。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)
