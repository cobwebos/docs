---
title: 如何面向 Azure Functions 运行时版本
description: Azure Functions 支持多个版本的运行时。 了解如何在 Azure 中指定函数应用的运行时版本。
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 6f93ac7bcbd25c1b120cfeecae9dd4353524855f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230326"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 There are two major versions: [1.x and 2.x](functions-versions.md), with version 3.x in preview. 默认情况下，函数应用基于 2.x 版的运行时创建。 本文介绍如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

## <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新

Azure Functions lets you target a specific version of the runtime by using the `FUNCTIONS_EXTENSION_VERSION` application setting in a function app. 函数应用将一直保留在指定的主版本上，直到显式选择迁移到新版本。

If you specify only the major version, the function app is automatically updated to new minor versions of the runtime when they become available. 新次要版本不会引入重大更改。 如果指定次要版本（例如，“2.0.12345”），则函数应用将被固定到该特定版本，直到显式更改它。

> [!NOTE]
> If you pin to a specific version of Azure Functions, and then try to publish to Azure using Visual Studio, a dialog window will pop up prompting you to update to the latest version or cancel the publish. To avoid this, add the `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` property in your `.csproj` file.

有新版本公开可用时，门户中将显示提示，让你可以升级到该版本。 升级至新版本后，始终可使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置移回到之前的版本。

The following table shows the `FUNCTIONS_EXTENSION_VERSION` values for each major version to enable automatic updates:

| 主版本 | `FUNCTIONS_EXTENSION_VERSION` value |
| ------------- | ----------------------------------- |
| 3.x (preview) | `~3` |
| 2.x  | `~2` |
| 1.x | `~1` |

更改运行时版本会导致函数应用重启。

## <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

You can change the runtime version used by your function app. Because of the potential of breaking changes, you can only change the runtime version before you have created any functions in your function app. 

> [!IMPORTANT]
> 虽然运行时版本由 `FUNCTIONS_EXTENSION_VERSION` 设置决定，但你应该在 Azure 门户中进行此更改，而不是直接更改设置。 这是因为该门户会验证你的更改，并根据需要进行其他相关更改。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Using the Azure portal, you can't change the runtime version for a function app that already contains functions.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>通过 Azure CLI

还可以通过 Azure CLI 查看和更新 `FUNCTIONS_EXTENSION_VERSION`。

>[!NOTE]
>因为运行时版本可能会影响其他设置，因此，应当在门户中更改版本。 当你更改运行时版本时，门户会自动进行其他必需的更新，例如 Node.js 版本和运行时堆栈。  

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
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

将 `<function_app>` 替换为你的函数应用的名称。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 另外，将 `<version>` 替换为 1.x 运行时的有效版本或版本 2.x 的 `~2`。

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az-login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)
