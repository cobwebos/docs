<properties
   pageTitle="Azure Batch PowerShell 入门 | Microsoft Azure"
   description="快速介绍可用于管理 Azure Batch 服务的 Azure PowerShell cmdlet"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="07/28/2016"
   ms.author="danlep"/>

# Azure 批处理 PowerShell cmdlet 入门
通过 Azure Batch PowerShell cmdlet，你可以执行许多与通过 Batch API、Azure 门户和 Azure 命令行界面执行的相同任务并为它们编写脚本。本文将简要介绍可用于管理 Batch 帐户和处理 Batch 资源（例如池、作业和任务）的 cmdlet。本文基于 Azure PowerShell 1.6.0 版本中的 cmdlet。

如需 Batch cmdlet 的完整列表和详细的 cmdlet 语法，请参阅 [Azure Batch cmdlet 参考](https://msdn.microsoft.com/library/azure/mt125957.aspx)。


## 先决条件

* **Azure PowerShell** - 有关 Azure PowerShell 的下载和安装说明，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。
   
    * 由于 Azure Resource Manager 模块中随附了 Azure Batch cmdlet，你需要运行 **Login-AzureRmAccount** cmdlet 以连接到你的订阅。
    
    * 建议你经常更新 Azure PowerShell 以利用服务更新和增强功能。
    
* **注册到 Batch 提供程序命名空间（一次性操作）**- 在使用 Batch 帐户之前，你必须注册到 Batch 提供程序命名空间。此操作每个订阅仅需执行一次。运行以下 cmdlet：

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## 管理批处理帐户和密钥

### 创建批处理帐户

**New-AzureRmBatchAccount** 可在指定的资源组中创建新的 Batch 帐户。如果你没有资源组，可以通过运行 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) cmdlet 并在 **Location** 参数中指定一个 Azure 区域（例如“美国中部”）来创建资源组。例如：


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


然后，在资源组中创建新的 Batch 帐户，为 <*account\_name*> 中的帐户指定帐户名，还要指定资源组的位置和名称。创建 Batch 帐户可能需要一些时间才能完成。例如：


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] Batch 帐户名在资源组所在的 Azure 区域中必须唯一，长度为 3 到 24 个字符，并且只能包含小写字母和数字。

### 获取帐户访问密钥
**Get-AzureRmBatchAccountKeys** 显示与 Azure Batch 帐户关联的访问密钥。例如，运行以下命令可获取你创建的帐户的主要密钥和辅助密钥。

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### 生成新的访问密钥
**New-AzureRmBatchAccountKey** 为 Azure Batch 帐户生成新的主要帐户密钥或辅助帐户密钥。例如，若要为批处理帐户生成新的主要密钥，请键入：


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] 若要生成新的辅助密钥，请为 **KeyType** 参数指定“Secondary”。必须单独重新生成主要密钥和辅助密钥。

### 删除批处理帐户
**Remove-AzureRmBatchAccount** 删除 Batch 帐户。例如：


    Remove-AzureRmBatchAccount -AccountName <account_name>

出现提示时，确认你想要删除该帐户。帐户删除可能需要一段时间才能完成。

## 创建 BatchAccountContext 对象

若要在创建和管理池、作业、任务和其他资源时使用 Batch PowerShell cmdlet 进行身份验证，需先创建 BatchAccountContext 对象来存储你的帐户名和密钥：

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

将 BatchAccountContext 对象传入使用 **BatchContext** 参数的 cmdlet。

> [AZURE.NOTE] 默认情况下，帐户的主要密钥用于身份验证，但你可以通过更改 BatchAccountContext 对象的 **KeyInUse** 属性，显式选择要使用的密钥：`$context.KeyInUse = "Secondary"`



## 创建和修改 Batch 资源
使用 **New-AzureBatchPool**、**New-AzureBatchJob** 和 **New-AzureBatchTask** 等 cmdlet 在 Batch 帐户下创建资源。可以使用相应的 **Get-** 和 **Set-** cmdlet 来更新现有资源的属性，以及使用 **Remove-** cmdlet 来删除 Batch 帐户下的资源。

使用这些 cmdlet 时，除了传递 BatchContext 对象，还需要创建或传递包含详细的资源设置的对象，如以下示例所示。请参阅每个 cmdlet 的详细帮助说明来了解其他示例。

### 创建 Batch 池

创建或更新 Batch 池时，为计算节点上的操作系统选择云服务配置或虚拟机配置（请参阅 [Batch 功能概述](batch-api-basics.md#pool)）。你的选择将决定是使用其中一个 [Azure 来宾 OS 版本](../cloud-services/cloud-services-guestos-update-matrix.md#releases)还是 Azure 应用商店中其中一个受支持的 Linux 或 Windows VM 映像为你的计算节点创建映像。

运行 **New-AzureBatchPool** 时，传递 PSCloudServiceConfiguration 或 PSVirtualMachineConfiguration 对象中的操作系统设置。例如，以下 cmdlet 可以在云服务配置中新建包含小型计算节点的 Batch 池，这些节点是使用系列 3 最新操作系统版本 (Windows Server 2012) 映像的。在此，**CloudServiceConfiguration** 参数指定 *$configuration* 变量作为 PSCloudServiceConfiguration 对象。**BatchContext** 参数将先前定义的变量 *$context* 指定为 BatchAccountContext 对象。


    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")
    
    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

通过自动缩放公式确定新池中的目标计算节点数。在本示例中，公式为 **$TargetDedicated=4**，表示池中的计算节点数最多为 4。

## 查询池、作业、任务以及其他详细信息

使用 **Get-AzureBatchPool**、**Get-AzureBatchJob** 和 **Get-AzureBatchTask** 等 cmdlet 查询在 Batch 帐户下创建的实体。


### 查询数据

例如，使用 **Get-AzureBatchPools** 可查找池。假设你已将 BatchAccountContext 对象存储在 *$context* 中，则默认情况下，此 cmdlet 将查询帐户下的所有池：


    Get-AzureBatchPool -BatchContext $context

### 使用 OData 筛选器

你可以使用 **Filter** 参数提供一个 OData 筛选器，以便只查找所需的对象。例如，可以查找 ID 以“myPool”开头的所有池：


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


此方法的灵活性不如在本地管道中使用“Where-Object”。但是，该查询将直接发送到批处理服务，因此所有筛选都在服务器端发生，这可以节省 Internet 带宽。

### 使用 Id 参数

OData 筛选器的替代方法是使用 **Id** 参数。若要查询 ID 为“myPool”的特定池：


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


**Id** 参数仅支持全 ID 搜索，而不支持通配符或 OData 样式的筛选器。



### 使用 MaxCount 参数

默认情况下，每个 cmdlet 最多返回 1000 个对象。如果达到此限制，可以优化筛选器以返回更少的对象，或者使用 **MaxCount** 参数显式设置最大值。例如：


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

若要去除上限，请 **MaxCount** 设置为 0 或更小。

### 使用管道

批处理 cmdlet 可以利用 PowerShell 管道在 cmdlet 之间发送数据。这与指定参数的效果相同，但可以更方便地列出多个实体。例如，以下代码可查找你帐户下的所有任务：


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## 后续步骤
* 有关详细的 cmdlet 语法和示例，请参阅 [Azure Batch cmdlet reference](https://msdn.microsoft.com/library/azure/mt125957.aspx)（Azure Batch cmdlet 参考）。

* 有关如何减少项数和有关针对 Batch 查询返回的信息类型的详细信息，请参阅[有效地查询 Batch 服务](batch-efficient-list-queries.md)。

<!---HONumber=AcomDC_0921_2016-->