---
title: PowerShell 入门
description: 快速介绍可用于管理 Batch 资源的 Azure PowerShell cmdlet。
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom: seodec18
ms.openlocfilehash: b768fac7fa6fe0f4821a4fbaf5fa11414b10f81d
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995318"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>使用 PowerShell cmdlet 管理 Batch 资源

通过 Azure Batch PowerShell cmdlet，可以执行许多通过 Batch API、Azure 门户和 Azure 命令行界面 (CLI) 执行的任务并为它们编写脚本。 本文将简要介绍可用于管理 Batch 帐户和处理 Batch 资源（例如池、作业和任务）的 cmdlet。

如需 Batch cmdlet 的完整列表和详细的 cmdlet 语法，请参阅 [Azure Batch cmdlet 参考](/powershell/module/az.batch)。

本文基于 Az Batch 模块 1.0.0 中的 cmdlet。 建议经常更新 Azure PowerShell 模块以利用服务更新和增强功能。

## <a name="prerequisites"></a>必备条件

* [下载并配置 Azure PowerShell 模块](/powershell/azure/overview)。 若要安装特定的 Azure Batch 模块，例如预发行模块，请参阅 [PowerShell 库](https://www.powershellgallery.com/packages/Az.Batch/1.0.0)。

* 运行 **Connect-AzAccount** cmdlet 连接到订阅（Azure 资源管理器模块中随附了 Azure Batch cmdlet）：

  ```powershell
  Connect-AzAccount
  ```

* **注册到批处理提供程序命名空间**。 执行此操作时，只需**每个订阅一次**。
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>管理批处理帐户和密钥

### <a name="create-a-batch-account"></a>创建批处理帐户

**New-AzBatchAccount** 可在指定的资源组中创建 Batch 帐户。 如果没有资源组，可以运行 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建一个资源组。 在“位置”参数中指定一个 Azure 区域，如“美国中部”。  例如：

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

然后，在该资源组中创建一个 Batch 帐户。 为 <*account_name*> 中的帐户指定帐户名，并指定资源组的位置和名称。 创建 Batch 帐户可能需要一些时间才能完成。 例如：

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch 帐户名在资源组所在的 Azure 区域中必须唯一，长度为 3 到 24 个字符，并且只能包含小写字母和数字。

### <a name="get-account-access-keys"></a>获取帐户访问密钥

**Get-AzBatchAccountKeys** 显示与 Azure Batch 帐户关联的访问密钥。 例如，运行以下命令可获取所创建的帐户的主要密钥和辅助密钥。

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>生成新的访问密钥

**New-AzBatchAccountKey** 为 Azure Batch 帐户生成新的主要帐户密钥或辅助帐户密钥。 例如，若要为批处理帐户生成新的主要密钥，请键入：

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> 若要生成新的辅助密钥，请为 **KeyType** 参数指定“Secondary”。 必须单独重新生成主要密钥和辅助密钥。

### <a name="delete-a-batch-account"></a>删除批处理帐户

**Remove-AzBatchAccount** 删除 Batch 帐户。 例如：

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

出现提示时，确认你想要删除该帐户。 帐户删除可能需要一段时间才能完成。

## <a name="create-a-batchaccountcontext-object"></a>创建 BatchAccountContext 对象

可以通过共享密钥身份验证或 Azure Active Directory 身份验证进行身份验证，以便管理 Batch 资源。 若要使用 Batch PowerShell cmdlet 进行身份验证，需先创建 BatchAccountContext 对象来存储帐户凭据或标识。 将 BatchAccountContext 对象传入使用 **BatchContext** 参数的 cmdlet。

### <a name="shared-key-authentication"></a>共享密钥身份验证

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> 默认情况下，帐户的主要密钥用于身份验证，但可以通过更改 BatchAccountContext 对象的 **KeyInUse** 属性，显式选择要使用的密钥：`$context.KeyInUse = "Secondary"`。

### <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>创建和修改 Batch 资源

使用 **New-AzBatchPool**、**New-AzBatchJob** 和 **New-AzBatchTask** 等 cmdlet 在 Batch 帐户下创建资源。 可以使用相应的 **Get-** 和 **Set-** cmdlet 来更新现有资源的属性，以及使用 **Remove-** cmdlet 来删除批处理帐户下的资源。

使用这些 cmdlet 时，除了传递 BatchContext 对象，还需要创建或传递包含详细的资源设置的对象，如以下示例所示。 请参阅每个 cmdlet 的详细帮助说明来了解其他示例。

### <a name="create-a-batch-pool"></a>创建 Batch 池

创建或更新 Batch 池时，为计算节点上的操作系统选择云服务配置或虚拟机配置（请参阅 [Batch 功能概述](batch-api-basics.md#pool)）。 如果指定云服务配置，系统会使用一个 [Azure 来宾 OS 版本](../cloud-services/cloud-services-guestos-update-matrix.md#releases)为计算节点创建映像。 如果指定虚拟机配置，则可指定一个受支持的 Linux 或 Windows VM 映像（在 [Azure 虚拟机市场][vm_marketplace]中列出），或者提供已准备的自定义映像。

运行 **New-AzBatchPool**时，传递 PSCloudServiceConfiguration 或 PSVirtualMachineConfiguration 对象中的操作系统设置。 例如，以下代码片段可以在虚拟机配置中创建包含 Standard_A1 大小计算节点的 Batch 池，这些节点包含 Ubuntu Server 18.04-LTS 映像。 在这里，**VirtualMachineConfiguration** 参数指定 *$configuration* 变量作为 PSVirtualMachineConfiguration 对象。 **BatchContext** 参数将先前定义的变量 *$context* 指定为 BatchAccountContext 对象。

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

通过自动缩放公式计算新池中的目标计算节点数。 在本示例中，公式为 **$TargetDedicated=4**，表示池中的计算节点数最多为 4。

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>查询池、作业、任务以及其他详细信息

使用 **Get-AzBatchPool**、**Get-AzBatchJob** 和 **Get-AzBatchTask** 等 cmdlet 查询在 Batch 帐户下创建的实体。

### <a name="query-for-data"></a>查询数据

例如，使用 **Get-AzBatchPools** 可查找池。 假设已将 BatchAccountContext 对象存储在 *$context*中，则默认情况下，此 cmdlet 将查询帐户下的所有池：

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>使用 OData 筛选器

可以使用 **Filter** 参数提供一个 OData 筛选器，以便只查找所需的对象。 例如，可以查找 ID 以“myPool”开头的所有池：

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

此方法的灵活性不如在本地管道中使用“Where-Object”。 但是，该查询将直接发送到批处理服务，因此所有筛选都在服务器端发生，这可以节省 Internet 带宽。

### <a name="use-the-id-parameter"></a>使用 Id 参数

OData 筛选器的替代方法是使用 **Id** 参数。 若要查询 ID 为“myPool”的特定池：

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

**Id** 参数仅支持完整 ID 搜索，而不支持通配符或 OData 样式的筛选器。

### <a name="use-the-maxcount-parameter"></a>使用 MaxCount 参数

默认情况下，每个 cmdlet 最多返回 1000 个对象。 如果达到此限制，可以优化筛选器以返回更少的对象，或者使用 **MaxCount** 参数显式设置最大值。 例如：

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

若要去除上限，请 **MaxCount** 设置为 0 或更小。

### <a name="use-the-powershell-pipeline"></a>使用 PowerShell 管道

Batch cmdlet 使用 PowerShell 管道在 cmdlet 之间发送数据。 这与指定参数的效果相同，但可以更方便地使用多个实体。

例如，可以查找和显示帐户下的所有任务：

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

重新启动（重新引导）池中的每个计算节点：

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>应用程序包管理

应用程序包提供将应用程序部署到池中计算节点的简化方式。 使用批处理 PowerShell cmdlet，可以上传和管理批处理帐户中的应用程序包，以及将包版本部署到计算节点。

**创建** 应用程序：

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**添加** 应用程序包：

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

设置应用程序的**默认版本**：

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**列出**应用程序的包

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**删除**应用程序包

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**删除**应用程序

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> 在删除某个应用程序之前，必须删除该应用程序的所有应用程序包版本。 如果尝试删除当前具有应用程序包的应用程序，会发生“冲突”错误。

### <a name="deploy-an-application-package"></a>部署应用程序包

在创建池时，可以指定一个或多个要部署的应用程序包。 如果创建池时指定包，该包会在节点加入池时部署到每个节点。 将节点重新启动或重置映像时，也会部署包。

创建池时，请指定 `-ApplicationPackageReference` 选项，以便在池节点加入该池时，将应用程序包部署到这些节点。 首先，创建 **PSApplicationPackageReference** 对象，并使用应用程序 ID 和要部署到池中计算节点的包版本来配置该对象：

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

接下来，请创建池，并将包引用对象指定为 `ApplicationPackageReferences` 选项的参数：

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

有关应用程序包的详细信息，可参阅[使用 Batch 应用程序包将应用程序部署到计算节点](batch-application-packages.md)。

> [!IMPORTANT]
> 必须将一个 Azure 存储帐户链接到你的 Batch 帐户才能使用应用程序包。

### <a name="update-a-pools-application-packages"></a>更新池的应用程序包

若要更新分配到现有池的应用程序，请先创建包含所需的属性（应用程序 ID 和包版本）的 PSApplicationPackageReference 对象：

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

接下来，从批处理中获取池，清除所有现有包，添加新的包引用，并使用新的池设置更新批处理服务：

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

现已更新批处理服务中的池属性。 但是，要将新应用程序包真正部署到池中的计算节点，必须将这些节点重新启动或重置映像。 可以使用以下命令重新启动池中的每个节点：

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> 可将多个应用程序包部署到池中的计算节点。 如果想要*添加*应用程序包而不是替换当前部署的包，请省略上面的 `$pool.ApplicationPackageReferences.Clear()` 代码行。

## <a name="next-steps"></a>后续步骤

* 有关详细的 cmdlet 语法和示例，请参阅 [Azure Batch cmdlet reference](/powershell/module/az.batch)（Azure Batch cmdlet 参考）。
* 有关 Batch 中的应用程序和应用程序包的详细信息，请参阅[使用 Batch 应用程序包将应用程序部署到计算节点](batch-application-packages.md)。

[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
