---
title: 教程 - 将变量添加到模板
description: 将变量添加到 Azure 资源管理器模板以简化语法。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a27022f8c06449d8b05f90f78055b983bc88c01c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471522"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>教程：将变量添加到资源管理器模板

本教程介绍如何将变量添加到模板。 变量可以简化模板。有了变量，你只需编写一次表达式，然后即可在模板中重复使用该表达式。 完成本教程需要 **7 分钟**。

## <a name="prerequisites"></a>必备条件

建议完成[有关函数的教程](template-tutorial-add-functions.md)，但这不是必需的。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

在上一篇教程的结束时，模板包含以下 JSON：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

存储帐户名称的参数难以使用，因为你必须提供唯一名称。 如果已完成本系列中的前述教程，你可能已厌倦了猜测某个唯一的名称。 若要解决此问题，可以添加一个变量，以便为存储帐户构造唯一名称。

## <a name="use-variable"></a>使用变量

以下示例突出显示了在将变量添加到模板时所做的更改，该模板用于创建唯一的存储帐户名称。 复制整个文件，将模板替换为该文件的内容。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

请注意，它包含一个名为 **uniqueStorageName** 的变量。 此变量使用四个函数来构造一个字符串值。

你已熟悉 [parameters](template-functions-deployment.md#parameters) 函数，因此我们不详细介绍它。

你也熟悉 [resourceGroup](template-functions-resource.md#resourcegroup) 函数。 在此示例中，你获得 **id** 属性而不是 **location** 属性，如上一教程所示。 **id** 属性返回资源组的完整标识符，包括订阅 ID 和资源组名称。

[uniqueString](template-functions-string.md#uniquestring) 函数创建一个 13 个字符的哈希值。 返回的值取决于传入的参数。 在本教程中，我们使用资源组 ID 作为哈希值的输入。 这意味着，我们可以将该模板部署到不同的资源组，获取不同的唯一字符串值。 但是，如果部署到同一资源组，则获得同一值。

[concat](template-functions-string.md#concat) 函数采用多个值并对其进行组合。 就此变量来说，它采用参数中的字符串和 uniqueString 函数中的字符串，并将二者组合成一个字符串。

可以通过 **storagePrefix** 参数传入一个用于标识存储帐户的前缀。 可以创建你自己的命名约定，以便在从长的资源列表完成部署后，通过该约定轻松地标识存储帐户。

最后请注意，存储帐户名称现在设置为变量而非参数。

## <a name="deploy-template"></a>部署模板

让我们部署该模板。 部署此模板比部署上述模板容易，因为你只提供存储名称的前缀。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假设已根据[第一篇教程](template-tutorial-create-first-template.md#deploy-template)中所述，将 **templateFile** 变量设置为模板文件的路径。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 可以看到，我们已部署一项存储帐户资源。 该存储帐户的名称为 **store** 加上一个由随机字符组成的字符串。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们添加了一个变量，用于创建存储帐户的唯一名称。 在下一教程中，我们从已部署的存储帐户返回一个值。

> [!div class="nextstepaction"]
> [添加输出](template-tutorial-add-outputs.md)
