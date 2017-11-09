---
title: "如何面向 Azure Functions 运行时版本 |Microsoft 文档"
description: "函数支持多个版本的运行时。 了解如何指定 Azure 托管的函数应用的运行时版本。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

Azure Functions 运行时在 Azure 中实现代码的无服务器执行。 你会在 Azure 中托管以外的各种环境中发现此运行时。 [Azure Functions 核心工具](functions-run-local.md)在开发计算机上执行此运行时。 [Azure Functions 运行时](functions-runtime-overview.md)允许在本地环境中使用函数。 

函数支持多个主版本的运行时。 主版本更新可以引入重大更改。 本主题介绍在 Azure 中托管时如何让函数应用面向特定的运行时版本。 

通过在函数应用中使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置，函数允许面向运行时的特定主版本。 这同时适用于公开版和预览版。 函数应用将保持在指定的主运行时版本上，直到显式选择升级至新版本。 将函数应用更新到运行时的新次要版本（一旦可用）。 次要版本更新不会引入重大更改。  

当有新的主版本公开发布后，在门户中查看函数应用时，将可以升级到此版本。 升级至新版本后，可始终使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置回到之前的运行时版本。

运行时版本的每一处更改都会导致函数应用重启。 所有运行时版本的发行说明均会公布在所有 [GitHub 存储库](https://github.com/Azure/azure-webjobs-sdk-script/releases)中。   
## <a name="view-the-current-runtime-version"></a>查看当前运行时版本

采用以下步骤查看函数应用当前使用的特定运行时版本。 

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“函数应用”，并在“已配置功能”下选择“函数应用设置”。 

    ![选择函数应用设置](./media/functions-versions/add-update-app-setting.png)

2. 在“函数应用设置”选项卡中，定位“运行时版本”。 请注意特定运行时版本和请求的主版本。 在以下示例中，主版本被设为 `~1.0`。
 
   ![选择函数应用设置](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>面向函数版本 2.0 运行时

>[!IMPORTANT]   
> Azure Functions 运行时 2.0 处于预览版阶段，目前 Azure Functions 的全部功能并非都可受到支持。 有关详细信息，请参阅 [Azure Functions 运行时 2.0 已知问题](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

可以在 Azure 门户中将函数应用升级至运行时 2.0 预览版。 在“函数应用设置”选项卡中，选择“运行时版本”下方的“beta”。  

   ![选择函数应用设置](./media/functions-versions/function-app-view-version.png)

此设置与设置 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置为 `beta` 的效果相同。 选择“~1”按钮回到当前公开支持的主版本。 另外，还可以使用 Azure CLI 更新此应用程序设置。 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>面向来自门户的特定运行时版本

如果需要面向主版本而不是当前主版本或版本 2.0 时，则必须设置 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“函数应用”，并在“已配置功能”下选择“应用程序设置”。

    ![选择函数应用设置](./media/functions-versions/add-update-app-setting1a.png)

2. 在“应用程序设置”选项卡中，查找 `FUNCTIONS_EXTENSION_VERSION` 设置，并将此值更改为版本 2.0 的 1.x 运行时或 `beta` 的有效主版本。 

    ![设置函数应用设置](./media/functions-versions/add-update-app-setting2.png)

3. 单击“保存”，以保持应用程序设置更新。 

## <a name="target-a-specific-version-using-azure-cli"></a>使用 Azure CLI 面向特定的版本

 还可以设置来自 Azure CLI 的 `FUNCTIONS_EXTENSION_VERSION`。 使用 Azure CLI 更新，通过 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令在函数应用中更新应用程序设置。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 用版本 2.0 的 1.x 运行时或 `beta` 的有效主版本替代 `<version>`。 

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure#az_login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。
