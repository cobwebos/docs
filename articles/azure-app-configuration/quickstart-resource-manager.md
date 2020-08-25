---
title: 有关自动完成使用 Azure 应用程序配置的 VM 部署的快速入门
description: 本快速入门演示如何使用 Azure PowerShell 模块和 Azure 资源管理器模板部署 Azure 应用程序配置存储。 然后使用该存储中的值来部署 VM。
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235173"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>快速入门：使用应用程序配置和资源管理器模板（ARM 模板）自动部署 VM

了解如何使用 Azure 资源管理器模板和 Azure PowerShell 部署 Azure 应用程序配置存储，如何将键值添加到存储中，以及如何使用该存储中的键值来部署 Azure 资源（例如本例中的 Azure 虚拟机）。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-templates"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)。 [第一个模板](https://azure.microsoft.comresources/templates/101-app-configuration-store/)创建应用程序配置存储：

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

模板中定义了一个 Azure 资源：

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores)：创建应用程序配置存储。

[第二个模板](https://azure.microsoft.com/resources/templates/101-app-configuration/)使用存储中的键值创建虚拟机。 在执行此步骤之前，需要使用门户或 Azure CLI 添加键值。

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>部署模板

### <a name="create-an-app-configuration-store"></a>创建应用配置存储区

1. 选择下图登录到 Azure 并打开一个模板。 该模板会创建应用程序配置存储。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. 选择或输入以下值。

    - 订阅：选择用于创建应用程序配置存储的 Azure 订阅。
    - 资源组：选择“新建”以创建新的资源组，除非你想使用现有资源组。
    - 区域：选择资源组的位置。  例如“美国东部”。
    - 配置存储名称：输入新的应用程序配置存储名称。
    - 位置：指定应用程序配置存储的位置。  使用默认值。
    - SKU 名称：指定应用程序配置存储的 SKU 名称。 使用默认值。

1. 选择“查看 + 创建”。
1. 验证该页是否显示“通过验证”，然后选择“创建” 。

记下资源组名称和应用程序配置存储名称。  部署虚拟机时需要这些值
### <a name="add-vm-configuration-key-values"></a>添加 VM 配置键-值

创建应用程序配置存储后，可以使用 Azure 门户或 Azure CLI 将键值添加到该存储。

1. 登录到 [Azure 门户](https://portal.azure.com)，然后导航到新创建的应用程序配置存储。
1. 从左侧菜单中选择“配置资源管理器”。
1. 选择“创建”以添加以下键值对：

   |密钥|值|Label|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   将“内容类型”保留为空。

若要使用 Azure CLI，请参阅[使用 Azure 应用程序配置存储中的键值](./scripts/cli-work-with-keys.md)。

### <a name="deploy-vm-using-stored-key-values"></a>使用存储的键-值部署 VM

将键-值添加到存储后，接下来可以使用 Azure 资源管理器模板部署 VM。 该模板引用创建的 **windowsOsVersion** 和 **diskSizeGB** 键。

> [!WARNING]
> ARM 模板无法引用已启用“专用链接”的应用配置存储中的密钥。

1. 选择下图登录到 Azure 并打开一个模板。 该模板使用应用程序配置存储中存储的键值创建虚拟机。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. 选择或输入以下值。

    - 订阅：选择用于创建虚拟机的 Azure 订阅。
    - 资源组：指定与应用程序配置存储相同的资源组，或选择“新建”以创建新的资源组。
    - 区域：选择资源组的位置。  例如“美国东部”。
    - 位置：指定虚拟机的位置。 使用默认值。
    - 管理员用户名：指定虚拟机的管理员用户名。
    - 管理员密码：指定虚拟机的管理员密码。
    - 域名标签：指定唯一的域名。
    - 存储帐户名称：为与虚拟机关联的存储帐户指定唯一的名称。
    - 应用程序配置存储资源组：指定包含应用程序配置存储的资源组。
    - 应用程序配置存储名称：指定 Azure 应用程序配置存储的名称。
    - VM SKU 密钥：指定 windowsOsVersion。  这是你添加到存储的密钥值名称。
    - 磁盘大小密钥：指定 diskSizeGB。 这是你添加到存储的密钥值名称。

1. 选择“查看 + 创建”。
1. 验证该页是否显示“通过验证”，然后选择“创建” 。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录到 [Azure 门户](https://portal.azure.com)，然后导航到新创建的虚拟机。
1. 从左侧菜单中选择“概述”，并验证“SKU”是否为“2019-Datacenter”  。
1. 从左侧菜单中选择“磁盘”，并验证数据磁盘的大小是否为“2013” 。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中所述的资源组、应用程序配置存储、VM 和所有相关资源，请将其删除。 如果你打算将来还要使用该应用程序配置存储或 VM，可以保留这些资源。 如果不打算继续使用该作业，请运行以下 cmdlet，删除本快速入门创建的所有资源：

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure 资源管理器模板和 Azure 应用程序配置中的键-值部署了一个 VM。

若要了解如何创建使用 Azure 应用程序配置的其他应用程序，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [快速入门：创建使用 Azure 应用程序配置的 ASP.NET Core 应用](quickstart-aspnet-core-app.md)
