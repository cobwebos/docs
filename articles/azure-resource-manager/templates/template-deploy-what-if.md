---
title: 模板部署 what-if（预览版）
description: 在部署 Azure 资源管理器模板之前确定资源将会发生的更改。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: tomfitz
ms.openlocfilehash: 27efe1e03b8a0d373d566106a53a41007731973e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810065"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 模板部署 what-if 操作（预览版）

 (ARM 模板) 部署 Azure 资源管理器模板之前，可以预览将发生的更改。 Azure 资源管理器提供 what-if（假设）操作，让你在部署模板时了解资源发生的更改。 what-if 操作不会对现有资源进行任何更改， 而是预测在部署指定的模板时发生的更改。

> [!NOTE]
> what-if 操作目前以预览版提供。 在预览版中，结果有时可能会显示资源将发生更改，但实际上并不会发生更改。 我们正在努力减少这些问题，但需要大家的帮助。 请在 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 上报告这些问题。

可将 what-if 操作与 Azure PowerShell、Azure CLI 或 REST API 操作配合使用。 资源组、订阅、管理组和租户级别部署支持的假设。

## <a name="install-azure-powershell-module"></a>安装 Azure PowerShell 模块

若要在 PowerShell 中使用 what-if，则必须安装 Az 模块 4.2 或更高版本。

但在安装所需模块之前，请确保安装了 PowerShell Core（6.x 或 7.x）。 如果安装了 PowerShell 5.x 或更早版本，请[更新 PowerShell 版本](/powershell/scripting/install/installing-powershell)。 无法在 PowerShell 5.x 或更早版本上安装所需模块。

### <a name="install-latest-version"></a>安装最新版本

若要安装该模块，请使用：

```powershell
Install-Module -Name Az -Force
```

若要详细了解如何安装模块，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

### <a name="uninstall-alpha-version"></a>卸载 alpha 版本

如果以前安装了 alpha 版本的 what-if 模块，请卸载该模块。 alpha 版本仅适用于注册了抢鲜预览版的用户。 如果未安装该预览版，则可跳过此部分。

1. 以管理员身份运行 PowerShell
1. 检查安装的 Az.Resources 模块版本。

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. 如果已安装版本的版本号格式为 2.x.x-alpha，请卸载该版本。

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. 取消注册用于安装预览版的 what-if 存储库。

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

现在可以使用 what-if 了。

## <a name="install-azure-cli-module"></a>安装 Azure CLI 模块

若要在 Azure CLI 中使用 what-if，则必须安装 Azure CLI 2.5.0 或更高版本。 如果需要，请[安装 Azure CLI 的最新版本](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>查看结果

在 PowerShell 或 Azure CLI 中使用 what-if 时，输出包含进行了颜色编码的结果，方便你查看不同类型的更改。

![资源管理器模板部署 what-if 操作 fullresourcepayloads 和更改类型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本输出如下：

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

> [!NOTE]
> 假设操作无法解析[reference 函数](template-functions-resource.md#reference)。 每次将属性设置为包含 reference 函数的模板表达式时，属性将更改的假设报告。 发生此行为的原因是，if 将属性的当前值（如）与 `true` `false` 未解析的模板表达式) 的布尔值进行比较 (。 很明显，这些值不匹配。 部署模板时，只有在模板表达式解析为其他值时，属性才会更改。

## <a name="what-if-commands"></a>what-if 命令

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署模板前预览更改，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 或 [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)。 将 `-Whatif` 开关参数添加到部署命令。

* 对于资源组部署，请使用 `New-AzResourceGroupDeployment -Whatif`
* 对于订阅级别的部署，请使用 `New-AzSubscriptionDeployment -Whatif` 和 `New-AzDeployment -Whatif`

可使用 `-Confirm` 开关参数来预览所做的更改，让系统显示是否继续部署的提示。

* 对于资源组部署，请使用 `New-AzResourceGroupDeployment -Confirm`
* 对于订阅级别的部署，请使用 `New-AzSubscriptionDeployment -Confirm` 和 `New-AzDeployment -Confirm`

上述命令返回适用于手动检查的文本摘要。 若要获取可通过编程方式检查更改的对象，请使用 [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) 或 [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)。

* 对于资源组部署，请使用 `$results = Get-AzResourceGroupDeploymentWhatIfResult`
* 对于订阅级别的部署，请使用 `$results = Get-AzSubscriptionDeploymentWhatIfResult` 或 `$results = Get-AzDeploymentWhatIfResult`

### <a name="azure-cli"></a>Azure CLI

若要在部署模板前预览更改，请使用：

* [az 部署组假设](/cli/azure/deployment/group#az-deployment-group-what-if)资源组部署
* [az deployment sub if](/cli/azure/deployment/sub#az-deployment-sub-what-if) for 订阅级别部署
* [az deployment mg](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-what-if)对于管理组部署
* [az deployment 租户](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-what-if)，适用于租户部署

可以使用 `--confirm-with-what-if` 开关（或其缩写形式 `-c`）预览更改，并让系统显示是否继续部署的提示。 将此开关添加到：

* [az 部署组创建](/cli/azure/deployment/group#az-deployment-group-create)
* [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)。
* [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az 部署租户创建](/cli/azure/deployment/tenant#az-deployment-tenant-create)

例如，使用 `az deployment group create --confirm-with-what-if` 或 `-c` 进行资源组部署。

上述命令返回适用于手动检查的文本摘要。 若要获取可通过编程方式检查更改的 JSON 对象，请使用 `--no-pretty-print` 开关。 例如，将 `az deployment group what-if --no-pretty-print` 用于资源组部署。

若要返回没有颜色的结果，请打开 [Azure CLI 配置](/cli/azure/azure-cli-configuration)文件。 将“no_color”设置为“yes”。

### <a name="azure-rest-api"></a>Azure REST API

对于 REST API，请使用：

* 对于资源组部署，请使用[部署 - What If](/rest/api/resources/deployments/whatif)
* [部署-订阅部署的订阅范围 What If](/rest/api/resources/deployments/whatifatsubscriptionscope)
* 部署-管理组部署的[管理组范围 What If](/rest/api/resources/deployments/whatifatmanagementgroupscope)
* [部署-租户范围内](/rest/api/resources/deployments/whatifattenantscope)的租户部署 What If。

## <a name="change-types"></a>更改类型

what-if 操作列出六种不同的更改类型：

- **创建**：资源当前不存在，但已在模板中定义。 将创建该资源。

- **删除**：仅当为部署使用[完整模式](deployment-modes.md)时，此更改类型才适用。 资源存在，但未在模板中定义。 使用完整模式时，将删除该资源。 此更改类型仅包括[支持完整模式删除](complete-mode-deletion.md)的资源。

- **忽略**：资源存在，但未在模板中定义。 不会部署或修改资源。

- **无更改**：资源存在，且已在模板中定义。 将重新部署资源，但资源的属性不会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **修改**：资源存在，且已在模板中定义。 将重新部署资源，且资源的属性会更改。 当 [ResultFormat](#result-format) 设置为 `FullResourcePayloads`（默认值）时，将返回此更改类型。

- **部署**：资源存在，且已在模板中定义。 将重新部署资源。 资源的属性可能会更改，也可能不会更改。 当没有足够的信息来确定是否有任何属性发生更改时，操作将返回此更改类型。 仅当 [ResultFormat](#result-format) 设置为 `ResourceIdOnly` 时，才会看到此状况。

## <a name="result-format"></a>结果格式

控制返回的有关预测更改的详细信息级别。 可以使用两个选项：

* **FullResourcePayloads** - 返回将会更改的资源的列表，以及将会更改的属性的相关详细信息。
* **ResourceIdOnly** - 返回将会更改的资源列表

默认值为 FullResourcePayloads。

对于 PowerShell 部署命令，请使用 `-WhatIfResultFormat` 参数。 在编程对象命令中，使用 `ResultFormat` 参数。

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

为了了解 what-if 的工作原理，让我们运行一些测试。 首先，部署一个[用于创建虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 将使用此虚拟网络来测试 what-if 如何报告更改。

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

部署完成后，即可测试 what-if 操作。 这一次，将部署一个[用于更改虚拟网络的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 该模板中缺少一个原始标记，已删除了一个子网，并且已更改了地址前缀。

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

what-if 输出类似于：

![资源管理器模板部署 what-if 操作输出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本输出如下：

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

输出的底部显示了“已删除所有者”标记。 地址前缀已从 10.0.0.0/16 更改为 10.0.0.0/15。 已删除名为 subnet001 的子网。 请记住，未部署这些更改。 如果部署该模板，则可预览会发生的更改。

列出为已删除的某些属性实际上不会更改。 当属性不在模板中时，它们可能被错误地报告为已删除，但在部署过程中会自动设置为默认值。 此结果在 what-if 响应中被视为“干扰信息”。 最终部署的资源将具有为属性设置的值。 当 what-if 操作成熟时，将从结果中筛选出这些属性。

## <a name="programmatically-evaluate-what-if-results"></a>以编程方式评估 what-if 结果

现在，让我们将命令设置为变量，以编程方式评估 what-if 结果。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

可以看到每项更改的摘要。

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

若要在部署模板之前预览所做的更改，请在部署命令中使用 confirm 开关参数。 如果更改符合预期，请确认你想要完成此部署。

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

由于该模板中未定义任何资源，且部署模式设置为完成，因此会删除虚拟网络。

![资源管理器模板部署 what-if 操作输出 - 完整部署模式](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

文本输出如下：

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

你会看到预期的更改，并且可以确认你想要运行此部署。

## <a name="sdks"></a>SDK

可以通过 Azure Sdk 使用假设操作。

* 对于 Python，请使用[假设](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)。

* 对于 Java，请使用[DeploymentWhatIf 类](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable)。

* 对于 .NET，请使用[DeploymentWhatIf 类](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet)。

## <a name="next-steps"></a>后续步骤

- 如果发现 what-if 预览版提供了错误的结果，请在 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 上报告问题。
- 若要使用 Azure PowerShell 来部署模板，请参阅[使用 ARM 模板和 Azure PowerShell 来部署资源](deploy-powershell.md)。
- 若要使用 Azure CLI 部署模板，请参阅[使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)。
- 若要使用 REST 来部署模板，请参阅[使用 ARM 模板和资源管理器 REST API 来部署资源](deploy-rest.md)。
