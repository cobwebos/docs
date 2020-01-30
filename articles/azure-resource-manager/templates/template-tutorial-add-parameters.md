---
title: 教程 - 将参数添加到模板
description: 将参数添加到 Azure 资源管理器模板，使其可重复使用。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89101a96f4fc228e2d5c45d67e10b52ac5d8aa11
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773209"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>教程：将参数添加到资源管理器模板

在[前一篇教程](template-tutorial-add-resource.md)中，你已了解如何将存储帐户添加到模板，然后部署该模板。 本教程将介绍如何通过添加参数来改进模板。 完成本教程大约需要 **14 分钟**。

## <a name="prerequisites"></a>必备条件

建议完成[有关资源的教程](template-tutorial-add-resource.md)，但这不是一项要求。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

在上一篇教程的结束时，模板包含以下 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

可能已注意到此模板有一个问题。 存储帐户名称已经过硬编码。 每次只能使用此模板部署同一个存储帐户。 若要使用不同的名称部署存储帐户，必须创建新的模板，这显然不是实现自动化部署的实用方法。

## <a name="make-template-reusable"></a>使模板可重复使用

为了使模板可重复使用，让我们添加一个可用于传入存储帐户名称的参数。 以下示例中突出显示的 JSON 演示了模板中发生的更改。 **storageName** 参数已标识为字符串。 最大长度设置为 24 个字符，以防止名称过长。

复制整个文件，将模板替换为该文件的内容。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>部署模板

让我们部署该模板。 以下示例使用 Azure CLI 或 PowerShell 来部署模板。 请注意，需要为部署命令中的某个值提供存储帐户名称。 对于存储帐户名称，请提供前一篇教程中所用的相同名称。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假设已根据[第一篇教程](template-tutorial-create-first-template.md#deploy-template)中所述，将 **templateFile** 变量设置为模板文件的路径。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>了解资源更新

在上一部分，你已使用以前创建的同一名称部署了一个存储帐户。 你可能想要知道，重新部署会对该资源造成怎样的影响。

如果该资源已存在，并且在属性中未检测到任何更改，则表示未采取任何措施。 如果该资源已存在，并且某个属性已更改，则表示该资源已更新。 如果该资源不存在，将会创建它。

这种处理更新的方式意味着，模板可以包含 Azure 解决方案所需的所有资源。 可以放心地重新部署该模板，并可以知道，只在必要时才会更改或创建资源。 例如，如果已将文件添加到存储帐户，则可以重新部署存储帐户，而不会丢失这些文件。

## <a name="customize-by-environment"></a>按环境进行自定义

可以使用参数提供针对特定环境定制的值，以便自定义部署。 例如，可以根据是否要部署到开发、测试和生产环境，来传递不同的值。

前一个模板始终部署 Standard_LRS 存储帐户。 你可能希望能够根据环境灵活部署不同的 SKU。 以下示例演示了为 SKU 添加参数而需要做出的更改。 复制整个文件并将其粘贴到模板中。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

**storageSKU** 参数有默认值。 如果在部署过程中未指定值，将使用此默认值。 它还有允许值列表。 这些值与创建存储帐户所需的值相匹配。 你不希望模板用户传入没有作用的 SKU。

## <a name="redeploy-template"></a>重新部署模板

现在可以重新部署。 由于默认 SKU 设置为 **Standard_LRS**，因此不需要提供该参数的值。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

若要查看模板的灵活性，请重新部署。 这一次，请将 SKU 参数设置为 **Standard_GRS**。 可以传入新名称来创建不同的存储帐户，或者使用同一个名称来更新现有的存储帐户。 这两个选项都是有效的。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

最后，让我们再次运行测试，以查看在传入一个未包含在允许值中的 SKU 时会发生什么情况。 在本例中，我们将测试模板用户认为 **basic** 是一个 SKU 时的情景。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

该命令立即失败并出现一条错误消息，指出哪些值是允许的。 在部署开始之前，资源管理器会识别到该错误。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

你已通过添加参数改进了[第一篇教程](template-tutorial-create-first-template.md)中创建的模板。 下一篇教程将介绍模板函数。

> [!div class="nextstepaction"]
> [添加模板函数](template-tutorial-add-functions.md)