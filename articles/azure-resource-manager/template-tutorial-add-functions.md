---
title: 教程 - 将模板函数添加到 Azure 资源管理器模板
description: 将模板函数添加到 Azure 资源管理器模板以构造值。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca6e7f326ba67eac33e4dee5fa844d7676ab864b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963654"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>教程：将模板函数添加到资源管理器模板

本教程介绍如何将[模板函数](resource-group-template-functions.md)添加到模板。 我们使用函数来动态构造值。 除了这些系统提供的模板函数，还可以创建[用户定义的函数](./template-user-defined-functions.md)。 完成本教程需要 **7 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关参数的教程](template-tutorial-add-parameters.md)，但这不是必需的。

必须有包含资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-your-template"></a>审阅模板

在上一教程的末尾，模板有以下 JSON：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

存储帐户的位置硬编码为“美国东部”。  但是，可能需要将存储帐户部署到其他区域。 你会再次面对一个问题：模板缺乏灵活性。 可以添加一个位置参数，但理想的情况是默认值更理性，而不仅仅是一个硬编码值。

## <a name="use-function"></a>使用函数

如果已完成本系列的上一教程，则你已使用了函数。 在添加 **"[parameters('storageName')]"** 时，你使用了 [parameters](resource-group-template-functions-deployment.md#parameters) 函数。 括号表示其中的语法是一个[模板表达式](template-expressions.md)。 资源管理器会对语法进行解析，而不是将其视为文本值。

函数可以在部署过程中动态获取值，为模板增加了灵活性。 在本教程中，我们使用函数来获取用于部署的资源组的位置。

以下示例重点介绍添加名为 **location** 的参数需要做的更改。  参数默认值调用 [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) 函数。 此函数返回的对象描述用于部署的资源组。 对象上的一个属性是位置属性。 使用默认值时，存储帐户位置与资源组位置相同。 资源组中的资源不需共享同一位置。 也可根据需要提供不同的位置。

复制整个文件，将模板替换为该文件的内容。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>部署模板

在前面的教程中，你创建的存储帐户位于“美国东部”，但资源组创建于“美国中部”。 在本教程中，存储帐户与资源组在同一区域创建。 请使用位置的默认值，这样就不需提供该参数值。 必须为存储帐户提供新名称，因为是在另一位置创建存储帐户。 例如，使用 **store2** 而不是 **store1** 作为前缀。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假定你已将 **templateFile** 变量设置为模板文件的路径，如[第一个教程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-the-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 从左侧菜单中，选择“资源组”  。
1. 选择已将内容部署到其中的资源组。
1. 可以看到一项存储帐户资源已部署，且其位置与资源组一样。

## <a name="clean-up-resources"></a>清理资源

若要继续完成下一教程，则不需删除资源组。

如果现在要停下来，则可能需要通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们在为参数定义默认值时使用了一个函数。 在本系列教程中，我们将继续使用函数。 到本系列完成时，我们会将函数添加到模板的每个部分。

> [!div class="nextstepaction"]
> [添加变量](template-tutorial-add-variables.md)
