---
title: 模板部署假设（预览）
description: 部署 Azure 资源管理器模板之前，确定对资源所做的更改。
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388517"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>资源管理器模板部署假设操作（预览）

在部署模板之前，您可能希望预览将要发生的更改。 Azure 资源管理器提供了 "假设" 操作，可让你在部署模板时了解资源将如何更改。 假设操作不会对现有资源进行任何更改。 相反，它会在部署指定模板时预测更改。

> [!NOTE]
> 假设操作当前为预览版。 若要使用它，必须[注册预览版](https://aka.ms/armtemplatepreviews)。 作为预览版本，结果有时可能会显示资源在实际不会发生更改时将更改。 我们正在努力减少这些问题，但我们需要你的帮助。 请在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告这些问题。

可以使用 PowerShell 命令或 REST API 操作的假设操作。

在 PowerShell 中，输出包含颜色编码的结果，可帮助您查看不同类型的更改。

![资源管理器模板部署 fullresourcepayload 操作，并更改类型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本 ouptput 为：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>假设命令

可以将 Azure PowerShell 或 Azure REST API 用于假设操作。

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署模板之前查看更改的预览，请将 `-Whatif` 开关参数添加到部署命令。

* 资源组部署的 `New-AzResourceGroupDeployment -Whatif`
* 订阅级别部署的 `New-AzSubscriptionDeployment -Whatif` 和 `New-AzDeployment -Whatif`

或者，你可以使用 `-Confirm` 开关参数预览更改并提示你继续部署。

* 资源组部署的 `New-AzResourceGroupDeployment -Confirm`
* 订阅级别部署的 `New-AzSubscriptionDeployment -Confirm` 和 `New-AzDeployment -Confirm`

上述命令返回可手动检查的文本摘要。 若要获取可通过编程方式检查更改的对象，请使用：

* 资源组部署的 `$results = Get-AzResourceGroupDeploymentWhatIf`
* 订阅级别部署的 `$results = Get-AzSubscriptionDeploymentWhatIf` 或 `$results = Get-AzDeploymentWhatIf`

> [!NOTE]
> 在版本 2.0.1-alpha5 之前，你已使用 `New-AzDeploymentWhatIf` 命令。 此命令已替换为 `Get-AzDeploymentWhatIf`、`Get-AzResourceGroupDeploymentWhatIf`和 `Get-AzSubscriptionDeploymentWhatIf` 命令。 如果你使用的是早期版本，则需要更新该语法。 已移除 `-ScopeType` 参数。

### <a name="azure-rest-api"></a>Azure REST API

对于 REST API，请使用：

* [部署-](/rest/api/resources/deployments/whatif)资源组部署的 What If
* [部署-在订阅级别部署的订阅范围内 What If](/rest/api/resources/deployments/whatifatsubscriptionscope)

## <a name="change-types"></a>更改类型

假设操作列出了六种不同类型的更改：

- **创建**：该资源当前不存在，但已在模板中定义。 将创建资源。

- **删除**：只有在使用[完整模式](deployment-modes.md)进行部署时，此更改类型才适用。 资源存在，但未在模板中定义。 对于完整模式，将删除该资源。 此更改类型中仅包括[支持完整模式删除](complete-mode-deletion.md)的资源。

- **Ignore**：资源存在，但未在模板中定义。 不会部署或修改资源。

- **NoChange**：资源存在，并且在模板中定义。 资源将重新部署，但资源的属性不会更改。 当[ResultFormat](#result-format)设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **修改**：资源存在，并且在模板中定义。 资源将重新部署，资源的属性也将更改。 当[ResultFormat](#result-format)设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **部署**：资源存在，并且在模板中定义。 资源将重新部署。 资源的属性可能会更改，也可能不会更改。 当操作没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当[ResultFormat](#result-format)设置为 `ResourceIdOnly`时才会看到此条件。

## <a name="result-format"></a>结果格式

可以控制返回的有关预测更改的详细信息级别。 在部署命令（`New-Az*Deployment`）中，使用 **-WhatIfResultFormat**参数。 在编程对象命令（`Get-Az*DeploymentWhatIf`）中，使用**ResultFormat**参数。

将 format 参数设置为**FullResourcePayloads**可获取将更改的资源的列表，以及有关将更改的属性的详细信息。 将 format 参数设置为 " **ResourceIdOnly** " 可获取将更改的资源列表。 默认值为**FullResourcePayloads**。  

以下结果显示了两种不同的输出格式：

- 完整资源负载

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- 仅资源 ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>运行假设操作

### <a name="set-up-environment"></a>设置环境

若要查看具体的工作原理，让我们运行一些测试。 首先，部署[用于创建虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 你将使用此虚拟网络来测试如何报告更改。

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>测试修改

部署完成后，您就可以测试 "假设" 操作了。 此时将部署[更改虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少原始标记，子网已删除，地址前缀已更改。

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

假设输出类似于：

![资源管理器模板部署假设操作输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本输出为：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

请注意，输出的顶部定义了颜色以指示更改的类型。

在输出的底部，显示标记所有者已删除。 地址前缀从 10.0.0.0/16 更改为 10.0.0.0/15。 已删除名为 subnet001 的子网。 请记住，此更改并未实际部署。 你将看到在你部署模板时会发生的更改的预览。

列出为删除的某些属性不会实际更改。 当属性不在模板中时，属性可能会错误地报告为 "已删除"，但会在部署过程中自动设置为默认值。 此结果在 "假设" 响应中被视为 "干扰"。 最终部署的资源将具有为属性设置的值。 随着假设操作的成熟，将从结果中筛选出这些属性。

## <a name="programmatically-evaluate-what-if-results"></a>以编程方式计算假设结果

现在，让我们通过将命令设置为变量以编程方式计算假设结果。

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

您可以查看每个更改的摘要。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>确认删除

假设操作使用[部署模式](deployment-modes.md)支持。 设置为 "完成" 模式时，将删除不在模板中的资源。 下面的示例部署一个[模板，该模板没有](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)在完整模式下定义的资源。

若要在部署模板前预览更改，请将 `-Confirm` 开关参数用于部署命令。 如果所做的更改与预期不同，请确认你希望部署完成。

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

由于未在模板中定义资源并且部署模式设置为 "完成"，因此将删除该虚拟网络。

![资源管理器模板部署假设操作输出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

文本输出为：

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

你会看到预期的更改，并且可以确认你希望部署运行。

## <a name="next-steps"></a>后续步骤

- 如果注意到的预览版本中的结果不正确，请在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告问题。
- 若要 Azure PowerShell 部署模板，请参阅[部署具有资源管理器模板和 Azure PowerShell 的资源](deploy-powershell.md)。
- 若要将模板与 REST 一起部署，请参阅[部署具有资源管理器模板和资源管理器 REST API 的资源](deploy-rest.md)。
