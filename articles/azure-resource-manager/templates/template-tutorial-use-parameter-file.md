---
title: 教程 - 使用参数文件部署模板
description: 使用参数文件，其中包含的值可用于部署 Azure 资源管理器模板。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6a12d92c0cfb9d86ebf4c335c351944997f79b4e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773148"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>教程：使用参数文件部署资源管理器模板

本教程介绍如何使用[参数文件](parameter-files.md)存储在部署过程中传递的值。 在以前的教程中，我们通过部署命令使用了内联参数。 此方法适用于测试模板，但在自动进行部署时，为环境传递一组值可能会更容易。 参数文件可以方便你针对特定的环境将参数值打包。 在本教程中，我们将针对开发和生产环境创建参数文件。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>必备条件

建议完成[有关标记的教程](template-tutorial-add-tags.md)，但这不是必需的。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

模板有许多可以在部署过程中提供的参数。 在上一教程的末尾，模板如下所示：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

该模板可以正常工作，但现在，你希望能够轻松地管理为模板传入的参数。

## <a name="add-parameter-files"></a>添加参数文件

参数文件是 JSON 文件，其结构类似于模板。 在文件中，我们提供需要在部署过程中传入的参数值。

在 VS Code 中，创建包含以下内容的新文件。 使用名称 **azuredeploy.parameters.dev.json** 保存文件。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

此文件是用于开发环境的参数文件。 请注意，它将 Standard_LRS 用于存储帐户，使用 **dev** 前缀为资源命名，并将 **Environment** 标记设置为 **Dev**。

同样，创建包含以下内容的新文件。 使用名称 **azuredeploy.parameters.prod.json** 保存文件。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

此文件是用于生产环境的参数文件。 请注意，它将 Standard_GRS 用于存储帐户，使用 **contoso** 前缀为资源命名，并将 **Environment** 标记设置为 **Production**。 在实际生产环境中，还需要将应用服务与非免费版 SKU 配合使用，但在本教程中，我们将继续使用该免费版 SKU。

## <a name="deploy-template"></a>部署模板

使用 Azure CLI 或 Azure PowerShell 来部署模板。

让我们创建两个新的资源组，对模板进行最终测试。 一个用于开发环境，一个用于生产环境。

首先，我们部署到开发环境。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

现在，我们部署到生产环境。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 可以看到在本教程中部署的两个新资源组。
1. 选择任一资源组，查看部署的资源。 请注意，这些资源与我们在参数文件中为该环境指定的值匹配。

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。  如果已完成此系列，则需删除三个资源组 - myResourceGroup、myResourceGroupDev 和 myResourceGroupProd。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

恭喜！你已完成本简介，知道如何将模板部署到 Azure 了。 如果你有任何意见和建议，请在反馈部分告知我们。 谢谢！

你现在可以学习有关模板的更高级概念了。 下一教程会更详细地探讨如何根据模板参考文档来定义要部署的资源。

> [!div class="nextstepaction"]
> [利用模板参考](template-tutorial-create-encrypted-storage-accounts.md)
