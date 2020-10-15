---
title: 教程 - 使用参数文件部署模板
description: 使用参数文件，其中包含的值可用于部署 Azure 资源管理器模板。
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: de72f9f32a3b08ad1742ee2055efce5b93cab899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069503"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>教程：使用参数文件部 ARM 模板

本教程介绍如何使用[参数文件](parameter-files.md)存储在部署过程中传递的值。 在以前的教程中，我们通过部署命令使用了内联参数。 此方法适用于测试 Azure 资源管理器模板（ARM 模板），但是当自动执行部署时，可以更容易地为环境传递一组值。 参数文件可以方便你针对特定的环境将参数值打包。 在本教程中，我们将针对开发和生产环境创建参数文件。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关标记的教程](template-tutorial-add-tags.md)，但这不是必需的。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

模板有许多可以在部署过程中提供的参数。 在上一教程的末尾，模板如下所示：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

该模板可以正常工作，但现在，你希望能够轻松地管理为模板传入的参数。

## <a name="add-parameter-files"></a>添加参数文件

参数文件是 JSON 文件，其结构类似于模板。 在文件中，我们提供需要在部署过程中传入的参数值。

在参数文件中，为模板中的参数提供值。 参数文件中每个参数的名称必须与模板中的参数名称相匹配。 该名称不区分大小写，但为了方便查看匹配的值，我们建议你匹配模板中的大小写。

无需为每个参数提供值。 如果未指定的参数具有默认值，则在部署过程中将使用该值。 如果参数没有默认值，并且未在参数文件中指定值，系统会在部署过程中提示你提供值。

不能在参数文件中指定与模板中的参数名称不匹配的参数名称。 如果提供了未知参数，会收到错误。

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

现在，我们部署到生产环境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> 如果部署失败，请使用“详细”开关获取有关正在创建的资源的信息。 使用“调试”开关获取调试的详细信息。

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

下一个教程系列将详细介绍如何部署模板。

> [!div class="nextstepaction"]
> [部署本地模板](./deployment-tutorial-local-template.md)
