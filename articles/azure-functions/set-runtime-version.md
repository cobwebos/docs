---
title: 如何面向 Azure Functions 运行时版本
description: Azure Functions 支持多个版本的运行时。 了解如何在 Azure 中指定函数应用的运行时版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843385"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 有两个主版本：[1.x 和 2.x](functions-versions.md)。 本文介绍如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

>[!IMPORTANT]   
> Azure Functions 运行时 2.0 以预览版提供，目前不一定支持所有的 Azure Functions 功能。 有关详细信息，请参阅 [Azure Functions 运行时版本概述](functions-versions.md)。

## <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新

Functions 允许通过使用函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置，针对特定版本的运行时。 函数应用将一直保留在指定的主版本上，直到显式选择迁移到新版本。

如果仅指定主版本（对于 1.x 为“~1”；对于 2.x 为“beta”），当运行时的新次要版本可用时，函数应用将自动更新到该版本。 新次要版本不会引入重大更改。 如果指定次要版本（例如，“1.0.11360”），函数应用将一直保留在此版本上，直到显式更改它。 

有新版本公开可用时，门户中将显示提示，让你可以升级到该版本。 升级至新版本后，始终可使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置移回到之前的版本。

更改运行时版本会导致函数应用重启。

可以在 `FUNCTIONS_EXTENSION_VERSION` 应用设置中为 1.x 运行时 启用自动更新设置的值当前为“~1”，为 2.x 运行时 启用自动更新设置的值为“beta”。

## <a name="view-the-current-runtime-version"></a>查看当前运行时版本

使用以下步骤查看函数应用当前使用的运行时版本。 

1. 在 [Azure 门户](https://portal.azure.com)中，导航到函数应用，并在“已配置功能”下选择“函数应用设置”。 

    ![选择函数应用设置](./media/functions-versions/add-update-app-setting.png)

2. 在“函数应用设置”选项卡中，定位“运行时版本”。 请注意特定运行时版本和请求的主版本。 在以下示例中，FUNCTIONS\_EXTENSION\_VERSION 应用设置设为 `~1`。
 
   ![选择函数应用设置](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>使用门户指定版本

如果需要指定的版本不是当前主版本或版本 2.0，请设置 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“函数应用”，并在“已配置功能”下选择“应用程序设置”。

    ![选择函数应用设置](./media/functions-versions/add-update-app-setting1a.png)

2. 在“应用程序设置”选项卡中，找到 `FUNCTIONS_EXTENSION_VERSION` 设置，并将此值更改为 1.x 运行时的有效版本或版本 2.0 的 `beta`。 波浪符加主要版本号表示使用该主要版本的最新版本（例如“~1”）。 

    ![设置函数应用设置](./media/functions-versions/add-update-app-setting2.png)

3. 单击“保存”，以保持应用程序设置更新。 

## <a name="target-a-version-using-azure-cli"></a>使用 Azure CLI 指定版本

 还可以设置来自 Azure CLI 的 `FUNCTIONS_EXTENSION_VERSION`。 使用 Azure CLI 更新，通过 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令在函数应用中更新应用程序设置。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 将 `<version>` 替换为 1.x 运行时的有效版本或版本 2.0 的 `beta`。 

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az_login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)