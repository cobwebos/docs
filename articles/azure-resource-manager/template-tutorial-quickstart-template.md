---
title: 教程 - 使用 Azure 快速入门模板 | Microsoft Docs
description: 了解如何使用 Azure 快速入门模板来完成模板开发。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a29d86d105579dda7c12b885e2977406f7b598a4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001478"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>教程：使用 Azure 快速入门模板

[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)是一个存储库，其中包含社区贡献的模板。 可以在模板开发中使用示例模板。 在本教程中，我们需找到一个网站资源定义，然后将其添加到自己的模板中。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关已导出模板的教程](template-tutorial-export-template.md)，但这不是必需的。

必须有包含资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-your-template"></a>审阅模板

在上一教程的末尾，模板有以下 JSON：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

此模板适用于部署存储帐户和应用服务计划，但你可能需要向其添加网站。 可以使用预生成的模板来快速发现部署资源所需的 JSON。

## <a name="find-a-template"></a>查找模板

1. 打开 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)
1. 在“搜索”中，输入“部署 linux web 应用”。  
1. 选择标题为“部署基本的 linux web 应用”的项。  如果找不到它，请单击此处的[直接链接](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)。
1. 选择“在 GitHub 上浏览”。 
1. 选择“azuredeploy.json”。 
1. 查看模板。 具体说来，请查找 `Microsoft.Web/sites` 资源。

    ![资源管理器模板快速入门网站](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>修订现有模板

将快速入门模板与现有模板合并：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

WebApp 名称必须在 Azure 中独一无二。 为了防止出现重复名称，我们已将 **webAppPortalName** 变量从 **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** 更新为 **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** 。

在 `Microsoft.Web/serverfarms` 定义末尾添加一个逗号，以便将资源定义与 `Microsoft.Web/sites` 定义分开。

在这个新资源中，有一些需要注意的重要功能。

你会注意到，它有一个名为 **dependsOn** 的元素，该元素设置为应用服务计划。 此设置是必需的，因为在创建 Web 应用之前，必须存在应用服务计划。 **dependsOn** 元素告知资源管理器如何将用于部署的资源排序。

**serverFarmId** 属性使用 [resourceId](resource-group-template-functions-resource.md#resourceid) 函数。 此函数获取资源的唯一标识符。 在此示例中，它获取应用服务计划的唯一标识符。 Web 应用与一个特定的应用服务计划相关联。

## <a name="deploy-the-template"></a>部署模板

使用 Azure CLI 或 Azure PowerShell 来部署模板。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假定你已将 **templateFile** 变量设置为模板文件的路径，如[第一个教程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>清理资源

若要继续完成下一教程，则不需删除资源组。

如果现在要停下来，则可能需要通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

我们学习了如何使用快速入门模板进行模板开发。 在下一教程中，我们向资源添加标记。

> [!div class="nextstepaction"]
> [添加标记](template-tutorial-add-tags.md)
