---
title: 教程 - 将资源添加到模板
description: 介绍创建第一个 Azure 资源管理器模板的步骤。 了解模板文件语法，以及如何部署存储帐户。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 924c369465bf53ea5f58de906bd0894ce822cac3
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148192"
---
# <a name="tutorial-add-a-resource-to-your-resource-manager-template"></a>教程：将资源添加到资源管理器模板

在[前一篇教程](template-tutorial-create-first-template.md)中，你已了解如何创建一个空白模板，然后部署该模板。 现在，可以部署实际的资源。 在本教程中，你将添加一个存储帐户。 完成本教程大约需要 **9 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关模板的简介教程](template-tutorial-create-first-template.md)，但这不是一项要求。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="add-resource"></a>添加资源

若要将存储帐户定义添加到现有模板，请查看以下示例中突出显示的 JSON。 可以复制整个文件并使用其内容替换模板，而无需尝试复制模板的各个节。

请将 **{provide-unique-name}** 替换为唯一的存储帐户名称。 存储帐户名称在 Azure 中必须是唯一的。 该名称只能包含小写字母或数字。 其长度不能超过 24 个字符。 可以尝试使用某种命名模式，例如，使用 **store1** 作为前缀，然后添加你的姓名首字母缩写和当天的日期。 例如，使用的名称类似于 **store1abc09092019**。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json?range=1-19&highlight=5-17)]

存储帐户的唯一名称不容易猜出，尤其是在大型部署的自动化中不太适合进行这种猜测。 稍后在本教程系列中，你将使用相应的模板功能来更轻松地创建唯一名称。

## <a name="resource-properties"></a>资源属性

你可能想要知道如何找到用于每种资源类型的属性。 可以使用[资源管理器模板参考](/azure/templates/)来查找你要部署的资源类型。

部署的每个资源至少具有以下三个属性：

- **类型**：资源的类型。 此值是资源提供程序的命名空间和资源类型（例如 Microsoft.Storage/storageAccounts）的组合。
- **apiVersion**：用于创建资源的 REST API 版本。 每个资源提供程序都发布了其自身的 API 版本，因此此值与特定的类型相关。
- **名称**：资源的名称。

大多数资源还有一个 **location** 属性，该属性用于设置资源部署到的区域。

其他属性因资源类型和 API 版本而异。 了解 API 版本与可用属性之间的关联非常重要，因此，让我们直接了解详情。

在本教程中，你已将一个存储帐户添加到了模板。 可以在 [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts) 中看到该 API 版本。 请注意，你并未将所有属性添加到模板。 许多属性是可选的。 Microsoft.Storage 资源提供程序可能发布了新的 API 版本，但要部署的版本不需要更改。 可以继续使用该版本，部署的结果是一致的。

如果查看较旧的 API 版本（例如 [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)），你会发现提供了一个较小的属性集。

如果你决定更改资源的 API 版本，请确保评估该版本的属性，并相应地调整模板。

## <a name="deploy-template"></a>部署模板

可以部署模板来创建存储帐户。 为部署指定不同的名称，以便可以在历史记录中轻松找到它。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假设已根据[第一篇教程](template-tutorial-create-first-template.md#deploy-template)中所述，将 **templateFile** 变量设置为模板文件的路径。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

你可能会遇到两种可能的部署失败：

- 错误：代码=AccountNameInvalid；消息={provide-unique-name} 不是有效的存储帐户名称。 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。

    在模板中，请将 **{provide-unique-name}** 替换为唯一的存储帐户名称。  请参阅[添加资源](#add-resource)。

- 错误：代码=StorageAccountAlreadyTaken；消息=名为 store1abc09092019 的存储帐户已被使用。

    在模板中尝试使用其他存储帐户名称。

由于创建了存储帐户，此部署花费的时间要长于空白模板部署。 这可能需要大约一分钟，但往往不需要这么长时间。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 可以看到，已部署了一个存储帐户。
1. 请注意，部署标签现在显示：“部署:  2 个已成功”。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

你已创建一个用于部署 Azure 存储帐户的简单模板。  在后面的教程中，你将了解如何向模板添加参数、变量、资源和输出。 这些功能是更复杂的模板的构建基块。

> [!div class="nextstepaction"]
> [添加参数](template-tutorial-add-parameters.md)