---
title: 模板部署 what-if（预览版）
description: 在部署 Azure 资源管理器模板之前确定资源将会发生的更改。
author: mumian
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: jgao
ms.openlocfilehash: 70023f4fa5d44c74c7ce14f3a2c09ff14c9d2f8c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581205"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 模板部署假设操作（预览）

在部署 Azure 资源管理器（ARM）模板之前，可以预览将发生的更改。 Azure 资源管理器提供 what-if（假设）操作，让你在部署模板时了解资源发生的更改。 what-if 操作不会对现有资源进行任何更改， 而是预测在部署指定的模板时发生的更改。

> [!NOTE]
> what-if 操作目前以预览版提供。 在预览版中，结果有时可能会显示资源将发生更改，但实际上并不会发生更改。 我们正在努力减少这些问题，但需要大家的帮助。 请在上[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告这些问题。

您可以使用 Azure PowerShell、Azure CLI 或 REST API 操作的假设操作。

## <a name="install-powershell-module"></a>安装 PowerShell 模块

若要在 PowerShell 中使用，你必须从 PowerShell 库安装 "Az .Resources module" 模块的预览版本。 但在安装该模块之前，请确保已安装 PowerShell Core （1.x 或 7. x）。 如果你安装了 PowerShell 1.x 或更早版本，请[更新你的 powershell 版本](/powershell/scripting/install/installing-powershell)。 无法在 PowerShell 1.x 或更早版本上安装预览模块。

### <a name="install-preview-version"></a>安装预览版本

若要安装预览版模块，请使用：

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>卸载 alpha 版本

如果以前安装了 "假设" 模块的 alpha 版本，请卸载该模块。 Alpha 版本仅适用于注册了早期预览版的用户。 如果未安装该预览版，则可以跳过此部分。

1. 以管理员身份运行 PowerShell
1. 检查已安装的 Az .Resources 模块版本。

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. 如果安装的版本的版本号采用了版本**2. x x alpha**，请卸载该版本。

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. 取消注册用于安装预览的 "假设" 存储库。

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

你可以使用假设。

## <a name="install-azure-cli-module"></a>安装 Azure CLI 模块

若要在 Azure CLI 中使用，则必须具有 Azure CLI 2.5.0 或更高版本。 如果需要，请[安装最新版本的 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>查看结果

当你在 PowerShell 或 Azure CLI 中使用 "假设" 时，输出将包括颜色编码的结果，可帮助你查看不同类型的更改。

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

## <a name="what-if-commands"></a>假设命令

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署模板前预览更改，请`-Whatif`将开关参数添加到部署命令。

* `New-AzResourceGroupDeployment -Whatif`对于资源组部署
* `New-AzSubscriptionDeployment -Whatif`订阅`New-AzDeployment -Whatif`级别部署的和

你可以使用`-Confirm`开关参数预览更改并提示你继续部署。

* `New-AzResourceGroupDeployment -Confirm`对于资源组部署
* `New-AzSubscriptionDeployment -Confirm`订阅`New-AzDeployment -Confirm`级别部署的和

上述命令返回可手动检查的文本摘要。 若要获取可通过编程方式检查更改的对象，请使用：

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`对于资源组部署
* `$results = Get-AzSubscriptionDeploymentWhatIfResult``$results = Get-AzDeploymentWhatIfResult`对于订阅级别部署

### <a name="azure-cli"></a>Azure CLI

若要在部署模板前预览更改， `what-if`请将与部署命令一起使用。

* `az deployment group what-if`对于资源组部署
* `az deployment sub what-if`对于订阅级别部署

你可以使用`--confirm-with-what-if`开关（或其简短形式`-c`）来预览更改并提示你继续部署。

* `az deployment group create --confirm-with-what-if`或`-c`用于资源组部署
* `az deployment sub create --confirm-with-what-if``-c`对于订阅级别部署

上述命令返回可手动检查的文本摘要。 若要获取可通过编程方式检查更改的 JSON 对象，请使用：

* `az deployment group what-if --no-pretty-print`对于资源组部署
* `az deployment sub what-if --no-pretty-print`对于订阅级别部署

### <a name="azure-rest-api"></a>Azure REST API

对于 REST API，请使用：

* [部署-](/rest/api/resources/deployments/whatif)资源组部署的 What If
* [部署-在订阅级别部署的订阅范围内 What If](/rest/api/resources/deployments/whatifatsubscriptionscope)

## <a name="change-types"></a>更改类型

what-if 操作列出六种不同的更改类型：

- **创建**：该资源当前不存在，但已在模板中定义。 将创建该资源。

- **删除**：只有在使用[完整模式](deployment-modes.md)进行部署时，此更改类型才适用。 资源存在，但未在模板中定义。 使用完整模式时，将删除该资源。 此更改类型仅包括[支持完整模式删除](complete-mode-deletion.md)的资源。

- **Ignore**：资源存在，但未在模板中定义。 不会部署或修改资源。

- **NoChange**：资源存在，并且在模板中定义。 将重新部署资源，但资源的属性不会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **修改**：资源存在，并且在模板中定义。 将重新部署资源，且资源的属性会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **部署**：资源存在，并且在模板中定义。 将重新部署资源。 资源的属性可能会更改，也可能不会更改。 当没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当 [ResultFormat](#result-format) 设置为 `ResourceIdOnly` 时，才会看到此状况。

## <a name="result-format"></a>结果格式

可以控制返回的有关预测更改的详细信息级别。 可以使用两个选项：

* **FullResourcePayloads** -返回将更改的资源的列表，以及有关将更改的属性的详细信息
* **ResourceIdOnly** -返回将更改的资源列表

默认值为**FullResourcePayloads**。

对于 PowerShell 部署命令，请使用`-WhatIfResultFormat`参数。 在编程对象命令中，使用`ResultFormat`参数。

对于 Azure CLI，请使用 `--result-format` 参数。
 
以下结果显示了两种不同的输出格式：

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

为了了解 what-if 的工作原理，让我们运行一些测试。 首先，部署[用于创建虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 你将使用此虚拟网络来测试如何报告更改。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>测试修改

部署完成后，即可测试 what-if 操作。 这次，你部署的[模板会更改虚拟网络](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少原始标记，已删除子网，并且地址前缀已更改。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

假设输出类似于：

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

在输出的底部，显示标记所有者已删除。 地址前缀从 10.0.0.0/16 更改为 10.0.0.0/15。 已删除名为 subnet001 的子网。 请记住，并未实际部署这些更改。 你将看到在你部署模板时会发生的更改的预览。

列出为已删除的某些属性实际上不会更改。 当属性不在模板中时，它们可能被错误地报告为已删除，但在部署过程中会自动设置为默认值。 此结果在 what-if 响应中被视为“干扰信息”。 最终部署的资源将具有为属性设置的值。 当 what-if 操作成熟时，将从结果中筛选出这些属性。

## <a name="programmatically-evaluate-what-if-results"></a>以编程方式计算假设结果

现在，让我们通过将命令设置为变量以编程方式计算假设结果。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>确认删除

what-if 操作支持使用[部署模式](deployment-modes.md)。 设置为完整模式时，将删除不在模板中的资源。 以下示例部署一个处于完整模式的[未定义任何资源的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

若要在部署模板前预览更改，请将 confirm 开关参数用于部署命令。 如果所做的更改与预期不同，请确认你希望部署完成。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

由于未在模板中定义资源并且部署模式设置为 "完成"，因此将删除该虚拟网络。

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

你会看到预期的更改，并且可以确认你希望部署运行。

## <a name="next-steps"></a>后续步骤

- 如果注意到的预览版本中的结果不正确，请在处[https://aka.ms/whatifissues](https://aka.ms/whatifissues)报告问题。
- 若要 Azure PowerShell 部署模板，请参阅[利用 ARM 模板部署资源和 Azure PowerShell](deploy-powershell.md)。
- 若要 Azure CLI 部署模板，请参阅[利用 ARM 模板部署资源和 Azure CLI](deploy-cli.md)。
- 若要将模板与 REST 一起部署，请参阅[利用 ARM 模板部署资源和资源管理器 REST API](deploy-rest.md)。
