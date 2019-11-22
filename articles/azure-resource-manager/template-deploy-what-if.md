---
title: 模板部署假设（预览）
description: 部署 Azure 资源管理器模板之前，确定对资源所做的更改。
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: 19cb674ca7a2dfefc11c7646b23427c722f6e671
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278301"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>资源管理器模板部署假设操作（预览）

在部署模板之前，您可能希望预览将要发生的更改。 Azure 资源管理器提供了 "假设" 操作，可让你在部署模板时了解资源将如何更改。 假设操作不会对现有资源进行任何更改。 相反，它会在部署指定模板时预测更改。

> [!NOTE]
> 假设操作当前为预览版。 若要使用它，必须[注册预览](https://aka.ms/armtemplatepreviews)。 作为预览版本，结果有时可能会显示资源在实际不会发生更改时将更改。 我们正在努力减少这些问题，但我们需要你的帮助。 请在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告这些问题。

可以通过 `New-AzDeploymentWhatIf` PowerShell 命令或[部署-What If](/rest/api/resources/deployments/whatif) REST 操作来使用 with if 操作。

在 PowerShell 中，输出如下所示：

![资源管理器模板部署 fullresourcepayload 操作，并更改类型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>更改类型

假设操作列出了六种不同类型的更改：

- **创建**：该资源当前不存在，但已在模板中定义。 将创建资源。

- **删除**：只有在使用[完整模式](deployment-modes.md)进行部署时，此更改类型才适用。 资源存在，但未在模板中定义。 对于完整模式，将删除该资源。 此更改类型中仅包括[支持完整模式删除](complete-mode-deletion.md)的资源。

- **Ignore**：资源存在，但未在模板中定义。 不会部署或修改资源。

- **NoChange**：资源存在，并且在模板中定义。 资源将重新部署，但资源的属性不会更改。 当[ResultFormat](#result-format)设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **修改**：资源存在，并且在模板中定义。 资源将重新部署，资源的属性也将更改。 当[ResultFormat](#result-format)设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **部署**：资源存在，并且在模板中定义。 资源将重新部署。 资源的属性可能会更改，也可能不会更改。 当操作没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当[ResultFormat](#result-format)设置为 `ResourceIdOnly`时才会看到此条件。

## <a name="deployment-scope"></a>部署范围

对于订阅或资源组级别的部署，可以使用 "假设" 操作。 你可以设置包含 `-ScopeType` 参数的部署范围。 接受的值为 `Subscription`，`ResourceGroup`。 本文介绍资源组部署。

若要了解订阅级别部署，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md#)。

## <a name="result-format"></a>结果格式

可以控制返回的有关预测更改的详细信息级别。 将 `ResultFormat` 参数设置为 `FullResourcePayloads` 以获取资源列表，其中会发生更改，以及有关将更改的属性的详细信息。 将 `ResultFormat` 参数设置为 `ResourceIdOnly` 以获取将更改的资源的列表。 默认值为 `FullResourcePayloads`。  

以下屏幕截图显示了两种不同的输出格式：

- 完整资源负载

    ![资源管理器模板部署假设操作 fullresourcepayloads 输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- 仅资源 ID

    ![资源管理器模板部署假设操作 resourceidonly 输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>运行假设操作

### <a name="set-up-environment"></a>设置环境

若要查看具体的工作原理，让我们运行一些测试。 首先，从[创建存储帐户的 Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)部署模板。 默认存储帐户类型为 `Standard_LRS`。 你将使用此存储帐户来测试如何报告更改。

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>测试修改

部署完成后，您就可以测试 "假设" 操作了。 运行 "模拟" 命令，但将存储帐户类型更改为 `Standard_GRS`。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

假设输出类似于：

![资源管理器模板部署假设操作输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

请注意，输出的顶部定义了颜色以指示更改的类型。

在输出的底部，显示 sku 名称（存储帐户类型）将从**Standard_LRS**更改为**Standard_GRS**。

列出为删除的某些属性不会实际更改。 在上图中，这些属性为 accessTier、keySource 和该部分中的其他属性。 当属性不在模板中时，属性可能会错误地报告为 "已删除"，但会在部署过程中自动设置为默认值。 此结果在 "假设" 响应中被视为 "干扰"。 最终部署的资源将具有为属性设置的值。 随着假设操作的成熟，将从结果中筛选出这些属性。

### <a name="test-deletion"></a>测试删除

假设操作使用[部署模式](deployment-modes.md)支持。 设置为 "完成" 模式时，将删除不在模板中的资源。 下面的示例部署一个[模板，该模板没有](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)在完整模式下定义的资源。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

由于未在模板中定义资源并且部署模式设置为 "完成"，因此将删除存储帐户。

![资源管理器模板部署假设操作输出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

请记住，如果不进行实际更改，这一点很重要。 资源组中仍存在该存储帐户。

## <a name="next-steps"></a>后续步骤

- 如果注意到的预览版本中的结果不正确，请在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告问题。
- 若要 Azure PowerShell 部署模板，请参阅[部署具有资源管理器模板和 Azure PowerShell 的资源](resource-group-template-deploy.md)。
- 若要将模板与 REST 一起部署，请参阅[部署具有资源管理器模板和资源管理器 REST API 的资源](resource-group-template-deploy-rest.md)。
- 若要在出现错误时回滚到成功的部署，请参阅[出错时回滚到成功部署](rollback-on-error.md)。
