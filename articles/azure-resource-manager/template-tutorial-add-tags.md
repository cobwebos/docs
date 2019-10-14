---
title: 教程 - 将标记添加到 Azure 资源管理器模板中的资源
description: 将标记添加到在 Azure 资源管理器模板中部署的资源。 可以通过标记对资源进行逻辑组织。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b6ff50f7254a51bcdf37ecb0afd8f0041a2c5da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177573"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>教程：在资源管理器模板中添加标记

本教程介绍如何将标记添加到模板中的资源。 可以通过[标记](resource-group-using-tags.md)对资源进行逻辑组织。 标记值显示在成本报告中。 完成本教程需要 **8 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关快速入门模板的教程](template-tutorial-quickstart-template.md)，但这不是必需的。

必须有包含资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-your-template"></a>审阅模板

以前的模板部署了存储帐户、应用服务计划和 Web 应用。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

部署这些资源以后，可能需要跟踪成本并找到属于某个类别的资源。 可以通过添加标记来解决这些问题。

## <a name="add-tags"></a>添加标记

可以通过对资源进行标记来添加有助于标识其用途的值。 例如，可以通过添加标记来列出环境和项目。 可以通过添加标记来确定某个成本中心或拥有该资源的团队。 添加对组织来说有意义的任何值。

以下示例突出显示了对模板所做的更改。 复制整个文件，将模板替换为该文件的内容。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-the-template"></a>部署模板

可以部署模板并查看结果了。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假定你已将 **templateFile** 变量设置为模板文件的路径，如[第一个教程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 从左侧菜单中，选择“资源组”  。
1. 选择已将内容部署到其中的资源组。
1. 选择一项资源，例如存储帐户资源。 可以看到，它现在有标记。

   ![显示标记](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>清理资源

若要继续完成下一教程，则不需删除资源组。

如果现在要停下来，则可能需要通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们向资源添加了标记。 下一教程介绍如何使用参数文件，以便简化将值传入模板的过程。

> [!div class="nextstepaction"]
> [使用参数文件](template-tutorial-use-parameter-file.md)
