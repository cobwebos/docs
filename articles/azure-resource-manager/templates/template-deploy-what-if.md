---
title: 模板部署 what-if（预览版）
description: 在部署 Azure 资源管理器模板之前确定资源将会发生的更改。
author: mumian
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: 9e0d0d572e08961b585a93e66e400b8c2e54bf7f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886834"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 模板部署操作（预览）

在部署 Azure 资源管理器 （ARM） 模板之前，可能需要预览将发生的更改。 Azure 资源管理器提供 what-if（假设）操作，让你在部署模板时了解资源发生的更改。 what-if 操作不会对现有资源进行任何更改， 而是预测在部署指定的模板时发生的更改。

> [!NOTE]
> what-if 操作目前以预览版提供。 在预览版中，结果有时可能会显示资源将发生更改，但实际上并不会发生更改。 我们正在努力减少这些问题，但需要大家的帮助。 请在 上[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告这些问题。

您可以将"如果"操作与 PowerShell 命令或 REST API 操作一起使用。

## <a name="install-powershell-module"></a>安装 PowerShell 模块

要在 PowerShell 中使用"如果"，请从 PowerShell 库中安装 Az.Resources 模块的预览版本。

### <a name="uninstall-alpha-version"></a>卸载 Alpha 版本

如果以前安装了假设模块的 Alpha 版本，请卸载该模块。 Alpha 版本仅适用于注册早期预览的用户。 如果未安装该预览版，则可以跳过此部分。

1. 以管理员身份运行 PowerShell
1. 检查已安装的 Az.Resources 模块版本。

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. 如果您有一个安装的版本，其版本号为**格式 2.x.x-alpha，** 请卸载该版本。

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. 取消注册用于安装预览的假设存储库。

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

### <a name="install-preview-version"></a>安装预览版本

要安装预览模块，请使用：

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

您已准备好使用"如果"

## <a name="see-results"></a>查看结果

在 PowerShell 中，输出包括颜色编码的结果，可帮助您查看不同类型的更改。

![资源管理器模板部署 what-if 操作 fullresourcepayloads 和更改类型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

## <a name="what-if-commands"></a>如果命令

可以使用 Azure PowerShell 或 Azure REST API 执行 If 操作。

### <a name="azure-powershell"></a>Azure PowerShell

要在部署模板之前查看更改的预览，请将`-Whatif`switch 参数添加到部署命令。

* `New-AzResourceGroupDeployment -Whatif`资源组部署
* `New-AzSubscriptionDeployment -Whatif`和`New-AzDeployment -Whatif`订阅级别部署

或者，您可以使用`-Confirm`Switch 参数预览更改，并提示您继续部署。

* `New-AzResourceGroupDeployment -Confirm`资源组部署
* `New-AzSubscriptionDeployment -Confirm`和`New-AzDeployment -Confirm`订阅级别部署

前面的命令返回可以手动检查的文本摘要。 要获取可以以编程方式检查更改的对象，请使用：

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`资源组部署
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`或`$results = Get-AzDeploymentWhatIfResult`用于订阅级别部署

### <a name="azure-rest-api"></a>Azure REST API

对于 REST API，请使用：

* [部署 -](/rest/api/resources/deployments/whatif)资源组部署的 IF
* [部署 - 订阅级别部署的订阅范围中"如果"内容](/rest/api/resources/deployments/whatifatsubscriptionscope)

## <a name="change-types"></a>更改类型

what-if 操作列出六种不同的更改类型：

- **创建**： 资源当前不存在，但在模板中定义。 将创建该资源。

- **删除**：此更改类型仅适用于使用[完整模式](deployment-modes.md)进行部署时。 资源存在，但未在模板中定义。 使用完整模式时，将删除该资源。 此更改类型仅包括[支持完整模式删除](complete-mode-deletion.md)的资源。

- **忽略**： 资源存在，但在模板中未定义。 不会部署或修改资源。

- **NoChange**： 资源存在，并在模板中定义。 将重新部署资源，但资源的属性不会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **修改**： 资源存在，并在模板中定义。 将重新部署资源，且资源的属性会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **部署**： 资源存在，并在模板中定义。 将重新部署资源。 资源的属性可能会更改，也可能不会更改。 当没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当 [ResultFormat](#result-format) 设置为 `ResourceIdOnly` 时，才会看到此状况。

## <a name="result-format"></a>结果格式

可以控制返回的有关所预测更改的详细级别。 在部署命令 （`New-Az*Deployment`中）中使用 **-WhatifResult格式**参数。 在编程对象命令 （`Get-Az*DeploymentWhatIf`中）中使用**结果格式**参数。

将格式参数设置为 **"完全资源有效负载"，** 以获取有关将更改的属性的资源列表和详细信息。 将格式参数设置为 **"仅资源 Id"** 以获取将更改的资源列表。 默认值为 **"完全资源有效负载**"。  

以下结果显示两种不同的输出格式：

- 完整资源有效负载

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

- 仅限资源 ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>运行 what-if 操作

### <a name="set-up-environment"></a>设置环境

为了了解 what-if 的工作原理，让我们运行一些测试。 首先，部署[创建虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 您将使用此虚拟网络来测试由 What-if 报告更改的方式。

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>测试修改

部署完成后，即可测试 what-if 操作。 这次部署一个[模板来更改虚拟网络](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少原始标记之一，子网已被删除，地址前缀已更改。

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

如果输出看起来类似于：

![资源管理器模板部署 what-if 操作输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

请注意，输出顶部的颜色用于指示更改类型。

在输出的底部，它显示标记所有者已被删除。 地址前缀从 10.0.0.0/16 更改为 10.0.0.0/15。 名为子网001的子网已被删除。 请记住，这些更改实际上未部署。 您将看到部署模板时将发生的更改的预览。

列出为已删除的某些属性实际上不会更改。 当属性不在模板中时，它们可能被错误地报告为已删除，但在部署过程中会自动设置为默认值。 此结果在 what-if 响应中被视为“干扰信息”。 最终部署的资源将具有为属性设置的值。 当 what-if 操作成熟时，将从结果中筛选出这些属性。

## <a name="programmatically-evaluate-what-if-results"></a>以编程方式评估"如果"结果

现在，让我们通过将命令设置为变量来以编程方式评估假设结果。

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

您可以看到每个更改的摘要。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>确认删除

what-if 操作支持使用[部署模式](deployment-modes.md)。 设置为完整模式时，将删除不在模板中的资源。 以下示例部署一个处于完整模式的[未定义任何资源的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

要在部署模板之前预览更改，`-Confirm`请使用具有部署命令的 switch 参数。 如果更改与预期相同，请确认您希望部署完成。

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

由于模板中未定义任何资源，并且部署模式设置为完成，因此虚拟网络将被删除。

![资源管理器模板部署 what-if 操作输出 - 完整部署模式](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

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

您将看到预期的更改，并可以确认您希望部署运行。

## <a name="next-steps"></a>后续步骤

- 如果您注意到 What-if 预览版本中的结果不正确，请在 上[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告问题。
- 要使用 Azure PowerShell 部署模板，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 要使用 REST 部署模板，请参阅[使用 ARM 模板和资源管理器 REST API 部署资源](deploy-rest.md)。
