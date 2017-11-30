---
title: "如何面向 Azure Functions 运行时版本"
description: "Azure Functions 支持多个版本的运行时。 了解如何指定 Azure 托管的函数应用的运行时版本。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 063232e40b30d03b0ee8b087a602fed0fee3be0a
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 有两个主版本：1.x 和 2.x。 本文介绍如何选择要使用的主版本以及如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

## <a name="differences-between-runtime-1x-and-2x"></a>运行时 1.x 和 2.x 之间的差异

> [!IMPORTANT] 
> 运行时 1.x 是批准在生产环境中使用的唯一版本。

| 运行时 | 状态 |
|---------|---------|
|1.x|正式发布 (GA)|
|2.x|预览|

以下部分介绍语言、绑定和跨平台开发支持方面的差异。

### <a name="languages"></a>语言

下表指示每个运行时版本支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

### <a name="bindings"></a>绑定 

运行时 1.x 支持的试验绑定在 2.x 中不可用。 有关 2.x 中的绑定支持和其他功能差异的信息，请参阅[运行时 2.0 已知问题](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。

运行时 2.x 可以创建自定义[绑定扩展](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。 使用此扩展性模型的内置绑定仅在 2.x 中可用；[Microsoft Graph 绑定](functions-bindings-microsoft-graph.md)是其中最早的内置绑定。

### <a name="cross-platform-development"></a>跨平台开发

运行时 1.x 仅在门户中或在 Windows 上支持函数开发；使用 2.x，可以在 Linux 或 macOS 上开发和运行 Azure Functions。

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

## <a name="target-the-version-20-runtime"></a>指定 2.0 版运行时

>[!IMPORTANT]   
> Azure Functions 运行时 2.0 以预览版提供，目前不一定支持所有的 Azure Functions 功能。 有关详细信息，请参阅本文前面的[运行时 1.x 和 2.x 之间的差异](#differences-between-runtime-1x-and-2x)。

可以在 Azure 门户中将函数应用升级至运行时 2.0 预览版。 在“函数应用设置”选项卡中，选择“运行时版本”下方的“beta”。  

![选择函数应用设置](./media/functions-versions/function-app-view-version.png)

此设置与设置 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置为 `beta` 的效果相同。 选择“~1”按钮回到当前公开支持的主版本。 另外，还可以使用 Azure CLI 更新此应用程序设置。 

## <a name="target-a-version-using-the-portal"></a>使用门户指定版本

如果需要指定的版本不是当前主版本或版本 2.0，则必须设置 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“函数应用”，并在“已配置功能”下选择“应用程序设置”。

    ![选择函数应用设置](./media/functions-versions/add-update-app-setting1a.png)

2. 在“应用程序设置”选项卡中，找到 `FUNCTIONS_EXTENSION_VERSION` 设置，并将此值更改为 1.x 运行时的有效版本或版本 2.0 的 `beta`。 

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

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure#az_login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)