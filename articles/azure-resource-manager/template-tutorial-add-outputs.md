---
title: 教程 - 将输出添加到 Azure 资源管理器模板
description: 将输出添加到 Azure 资源管理器模板以简化语法。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 458833372d5bd03a04e4df7d6e915cddb4bb05c7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001537"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>教程：将输出添加到资源管理器模板

本教程介绍如何从模板返回值。 需要已部署资源提供的值时，请使用输出。 完成本教程需要 **7 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关变量的教程](template-tutorial-add-variables.md)，但这不是必需的。

必须有包含资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-your-template"></a>审阅模板

在上一教程的末尾，模板有以下 JSON：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

它部署一个存储帐户，但不返回有关该存储帐户的任何信息。 可能需要从新资源捕获属性，以便以后引用它们。

## <a name="add-outputs"></a>添加输出

可以使用输出，这样就可以从模板返回值。 例如，可以获取新存储帐户的终结点。

以下示例重点介绍添加输出值时需要对模板进行的更改。 复制整个文件，将模板替换为该文件的内容。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

关于添加的输出值，有一些重要的值得注意的项。

返回的值的类型设置为 **object**，这意味着它会返回 JSON 对象。

它使用 [reference](resource-group-template-functions-resource.md#reference) 函数获取存储帐户的运行时状态。 若要获取资源的运行时状态，请传入资源的名称或 ID。 在此示例中，使用的变量与创建存储帐户名称时使用的变量相同。

最后，它从存储帐户返回 **primaryEndpoints** 属性

## <a name="deploy-the-template"></a>部署模板

现在可以部署模板并查看返回的值了。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假定你已将 **templateFile** 变量设置为模板文件的路径，如[第一个教程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

在部署命令的输出中，将会看到如下所示的对象：

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>回顾所做的工作

我们在已完成的六个教程中做了很多工作。 让我们抽些时间来回顾所做的工作。 我们创建了一个模板，其中的参数很容易提供。 该模板可以在不同环境中重复使用，因为它允许自定义，并且可以动态创建所需值。 它还返回有关存储帐户的信息，这些信息可以用在脚本中。

现在，让我们来看一下资源组和部署历史记录。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 从左侧菜单中，选择“资源组”  。
1. 选择已将内容部署到其中的资源组。
1. 我们会在资源组中有至少一个（也可能有多个）存储帐户，具体取决于所执行的步骤。
1. 此外还会在历史记录中列出多个成功的部署。 选择该链接。

   ![选择部署](./media/template-tutorial-add-outputs/select-deployments.png)

1. 可以在历史记录中看到所有部署。 选择名为 **addoutputs** 的部署。

   ![显示部署历史记录](./media/template-tutorial-add-outputs/show-history.png)

1. 可以查看输入。

   ![显示输入](./media/template-tutorial-add-outputs/show-inputs.png)

1. 可以查看输出。

   ![显示输出](./media/template-tutorial-add-outputs/show-outputs.png)

1. 可以查看模板。

   ![显示模板](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>清理资源

若要继续完成下一教程，则不需删除资源组。

如果现在要停下来，则可能需要通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们向模板添加了一个返回值。 在下一教程中，我们将了解如何导出模板，以及如何在模板中使用该导出的模板的部件。

> [!div class="nextstepaction"]
> [使用导出的模板](template-tutorial-export-template.md)
