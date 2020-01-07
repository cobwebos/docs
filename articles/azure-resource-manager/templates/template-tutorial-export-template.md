---
title: 教程 - 从 Azure 门户导出模板
description: 了解如何使用导出的模板完成模板开发。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c22a8e2b479ed7d3a225bc649d89f5302d0f536
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471122"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>教程：从 Azure 门户使用导出的模板

在本教程系列中，你已创建一个用于部署 Azure 存储帐户的模板。 在接下来的两篇教程中，你将添加一个应用服务计划和一个网站。   本教程介绍如何从 Azure 门户导出模板（无需从头开始创建模板），以及如何使用 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)中的示例模板。 你可以根据自己的用途自定义这些模板。 本教程重点介绍如何导出模板，以及自定义模板的结果。 完成本教程需要大约 **14 分钟**。

## <a name="prerequisites"></a>必备条件

建议完成[有关输出的教程](template-tutorial-add-outputs.md)，但这不是一项要求。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

在上一篇教程的结束时，模板包含以下 JSON：

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

此模板非常适合用于部署存储帐户，但可以在其中添加更多的资源。 可以从现有的资源导出模板，以快速获取该资源的 JSON。

## <a name="create-app-service-plan"></a>创建应用服务计划

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”。 
1. 在“搜索市场”中输入“应用服务计划”，然后选择“应用服务计划”。     请不要选择“应用服务计划(经典)” 
1. 选择“创建”  。
1. 输入：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：选择“新建”，然后指定名称。  提供的资源组名称应该与过去在本教程系列中使用的名称不同。
    - **名称**：输入应用服务计划的名称。
    - **操作系统**：选择“Linux”。 
    - **区域**：选择一个 Azure 位置。 例如“美国中部”。 
    - **定价层**：为了节省成本，请将 SKU 更改为“基本 B1”（在“开发/测试”下）。 

    ![资源管理器模板 - 在门户中导出模板](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. 选择“查看并创建”。 
1. 选择“创建”  。 创建资源需要花费片刻时间。

## <a name="export-template"></a>导出模板

1. 选择“转到资源”。 

    ![转到资源](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. 选择“导出模板”。 

    ![资源管理器模板 - 导出模板](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   导出模板功能将提取资源的当前状态，并生成用于部署该资源的模板。 导出模板可能有助于快速获取部署资源所需的 JSON。

1. 将 **Microsoft.Web/serverfarms** 定义和参数定义复制到模板。

    ![资源管理器模板 - 导出模板 - 导出的模板](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> 通常，导出的模板比创建模板时所需的信息更详细。 例如，导出的模板中的 SKU 对象包含五个属性。 此模板是可行的，但你只需使用 **name** 属性。 可以从导出的模板着手，然后根据要求对其进行修改。

## <a name="revise-existing-template"></a>修订现有模板

导出的模板提供所需的大部分 JSON，但你需要根据模板自定义这些 JSON。 请特别注意你的模板与导出的模板之间的参数和变量差异。 很明显，导出过程并不知道你已在模板中定义的参数和变量。

以下示例突出显示了在模板中添加的内容。 其中包含导出的代码以及一些更改。 第一，它会更改参数的名称以符合命名约定。 第二，它对应用服务计划的位置使用 location 参数。 第三，它会删除 **properties** 对象中的 **name**，因为在资源级别，此值对于 **name** 属性是多余的。

复制整个文件，将模板替换为该文件的内容。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-template"></a>部署模板

使用 Azure CLI 或 Azure PowerShell 来部署模板。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假设已根据[第一篇教程](template-tutorial-create-first-template.md#deploy-template)中所述，将 **templateFile** 变量设置为模板文件的路径。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
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
1. 该资源组包含一个存储帐户和一个应用服务计划。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

你已了解如何从 Azure 门户导出模板，以及如何使用导出的模板进行模板开发。 你还可以使用 Azure 快速入门模板来简化模板开发。

> [!div class="nextstepaction"]
> [使用 Azure 快速入门模板](template-tutorial-quickstart-template.md)
