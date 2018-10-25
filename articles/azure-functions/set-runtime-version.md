---
title: 如何面向 Azure Functions 运行时版本
description: Azure Functions 支持多个版本的运行时。 了解如何在 Azure 中指定函数应用的运行时版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886801"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 有两个主版本：[1.x 和 2.x](functions-versions.md)。 默认情况下，函数应用基于 2.x 版的运行时创建。 本文介绍如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

> [!NOTE]
> 无法为具有一个或多个函数的函数应用更改运行时版本。 应当使用 Azure 门户更改运行时版本。

## <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新

Functions 允许通过使用函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置，针对特定版本的运行时。 函数应用将一直保留在指定的主版本上，直到显式选择迁移到新版本。

如果仅指定主版本（对于 2.x 为“~2”；对于 1.x 为“~1”），当运行时的新次要版本可用时，函数应用将自动更新到该版本。 新次要版本不会引入重大更改。 如果指定次要版本（例如，“2.0.12345”），则函数应用将被固定到该特定版本，直到显式更改它。

有新版本公开可用时，门户中将显示提示，让你可以升级到该版本。 升级至新版本后，始终可使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置移回到之前的版本。

更改运行时版本会导致函数应用重启。

可以在 `FUNCTIONS_EXTENSION_VERSION` 应用设置中为 1.x 运行时 启用自动更新设置的值当前为“~1”，为 2.x 运行时 启用自动更新设置的值为“~2”。

## <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

使用以下步骤查看函数应用当前使用的运行时版本。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到你的函数应用。

1. 在“配置的功能”下，选择“函数应用设置”。

    ![选择函数应用设置](./media/set-runtime-version/add-update-app-setting.png)

1. 在“函数应用设置”选项卡中，定位“运行时版本”。 请注意特定运行时版本和请求的主版本。 在以下示例中，版本设置为 `~2`。

   ![选择函数应用设置](./media/set-runtime-version/function-app-view-version.png)

1. 若要将函数应用固定到 1.x 版运行时，请在“运行时版本”下选择“~1”。 当应用中存在函数时，此开关被禁用。

1. 如果更改了运行时版本，请返回到“概述”选项卡并选择“重启”以重启应用。  函数应用重新开始在 1.x 版运行时上运行，并且在创建函数时将使用 1.x 版模板。

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>使用 Azure CLI 查看和更新运行时版本

还可以通过 Azure CLI 查看和更新 `FUNCTIONS_EXTENSION_VERSION`。

>[!NOTE]
>因为运行时版本可能会影响其他设置，因此，应当在门户中更改版本。 当你更改运行时版本时，门户会自动进行其他必需的更新，例如 Node.js 版本和运行时堆栈。  

使用 Azure CLI 时，可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令查看当前的运行时版本。

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

可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令更新函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 设置。

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
