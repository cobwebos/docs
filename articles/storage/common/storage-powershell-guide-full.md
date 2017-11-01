---
title: "对 Azure 存储使用 Azure PowerShell | Microsoft Docs"
description: "了解如何对 Azure 存储使用 Azure PowerShell cmdlet。"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: a116b4c15046e704e374ca67c5695ff3f01ba7fb
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>对 Azure 存储 使用 Azure PowerShell

Azure PowerShell 用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 针对 Azure 存储，将这些 cmdlet 划分为两个类别 -- 控制平面和数据平面。 控制平面 cmdlet 用于管理存储帐户，即创建存储帐户、设置属性、删除存储帐户、轮换访问密钥等。 数据平面 cmdlet 用于管理存储帐户中存储的数据。 例如，上传 blob、创建文件共享以及将消息添加到队列。

本操作说明文章介绍了使用管理平面 cmdlet 管理存储帐户的常见操作。 学习如何： 

> [!div class="checklist"]
> * 列出存储器帐户
> * 获取对现有存储帐户的引用
> * 创建存储帐户 
> * 设置存储帐户属性
> * 检索和再生成访问密钥
> * 保护对存储帐户的访问 
> * 启用存储分析

本文还将提供指向用于存储的几个其他 PowerShell 文章的链接，例如，如何启用和访问存储分析，以及如何使用数据平面 cmdlet。
<!-- also how to access the china and government clouds  -->

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本演练需要 Azure PowerShell 模块 3.6 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

对于本演练，可以将命令键入到一个常规的 PowerShell 窗口中，也可以使用 [Windows PowerShell 集成脚本环境 (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) 并将命令键入到编辑器中，然后在浏览示例时测试一个或多个命令。 可以突出显示想要执行的行，并单击“运行所选项”来仅运行这些命令。

有关存储帐户的详细信息，请参阅[存储简介](storage-introduction.md)和[关于 Azure 存储帐户](storage-create-storage-account.md)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>列出订阅中的存储帐户

运行 [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) cmdlet 来检索当前订阅中的存储帐户列表。 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>获取对存储帐户的引用

接下来，需要对存储帐户的引用。 可以创建一个新存储帐户，也可以获取对现有存储帐户的引用。 下列各部分将演示这两种方法。 

### <a name="use-an-existing-storage-account"></a>使用现有的存储帐户 

若要检索现有的存储帐户，则需要资源组的名称和存储帐户的名称。 为这两个字段设置变量，然后使用 [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) cmdlet。 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

现在，你已有指向现有存储帐户的 $storageAccount。

### <a name="create-a-storage-account"></a>创建存储帐户 

以下脚本将演示如何使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 创建常规用途的存储帐户。 创建帐户后，检索其上下文，该操作可以在后续命令中使用，而不针对每次调用指定身份验证。

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard\_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

该脚本使用以下 PowerShell cmdlet： 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) -- 检索有效位置的列表。 该示例使用 `eastus` 作为位置。

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) -- 创建新资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 我们的资源组称为 `teststoragerg`。 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) -- 创建实际存储帐户。 该示例使用 `testpshstorage`。

SKU 名称指示用于存储帐户的复制类型，如 LRS（本地冗余存储）。 有关复制的详细信息，请参阅 [Azure 存储复制](storage-redundancy.md)。

> [!IMPORTANT]
> 存储帐户的名称在 Azure 中是唯一的，并且必须采用小写。 有关命名约定和限制的信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。
> 

现在，你有新的存储帐户以及对它的引用。 

## <a name="managing-the-storage-account"></a>管理存储帐户

现在，已有对新存储帐户或现有存储帐户的引用，以下部分将介绍一些可用于管理存储帐户的命令。

### <a name="storage-account-properties"></a>存储帐户属性

若要更改存储帐户的设置，请使用 [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount)。 虽然无法更改存储帐户的位置或该帐户所在的资源组，但可以更改许多其他属性。 下面列出一些可使用 PowerShell 更改的属性。

* 分配给存储帐户的自定义域。

* 分配给存储帐户的标记。 标记通常用于分类资源以进行计费。

* SKU 是存储帐户的复制设置，例如 LRS（对于本地冗余存储）。 例如，可能会从标准\_LRS 更改为标准\_GRS 或标准\_RAGRS。 请注意，无法将标准 ZRS 或高级 LRS 更改为其他 SKU，反之亦然。 

* Blob 存储帐户的访问层。 将访问层的值设置为“热”或“冷”，并允许用户通过选择符合存储帐户使用方式的访问层来最大限度地降低成本。 有关详细信息，请参阅[热、冷存储层和存档存储层](../blobs/storage-blob-storage-tiers.md)。

* Blob 存储和/或文件存储的存储服务加密设置。 有关 SSE 的详细信息，请参阅[存储服务加密](storage-service-encryption.md)。

* 仅允许 HTTPS 流量。 

### <a name="managing-the-access-keys"></a>管理访问密钥

Azure 存储帐户附带了两个帐户密钥。 若要检索密钥，请使用 [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey)。 此示例将检索第一个密钥。 若要检索另一个密钥，请使用 `Value[1]` 而不是 `Value[0]`。

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

若要再生成密钥，请使用 [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey)。 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

若要再生成另一个密钥，请将 `key2` 用作密钥名称，而不是 `key1`。

再生成其中一个密钥，然后再次对其进行检索以查看新值。

> [!NOTE] 
> 为生产存储帐户再生成密钥之前，应进行仔细的规划。 再生成一个或两个密钥将无法再访问使用已再生成密钥的任何应用程序。 有关详细信息，请参阅[再生成存储访问密钥](storage-create-storage-account.md#regenerate-storage-access-keys)。


### <a name="delete-a-storage-account"></a>删除存储帐户 

若要删除存储帐户，请使用 [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount)。 

> [!IMPORTANT]
> 在删除存储帐户时，还会删除该帐户中存储的所有资产。 如果意外删除某个帐户，请立即致电支持人员，并打开票证以还原该存储帐户。 不保证数据能得以恢复，但有时上述操作能起作用。 在支持票证得到解决之前，请不要使用相同的旧帐户名创建新的存储帐户。 
>

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

### <a name="protecting-your-storage-account-using-vnets-and-firewalls"></a>使用 Vnet 和防火墙来保护存储帐户

默认情况下，所有存储帐户均可通过任何有权访问 Internet 的网络进行访问。 但是，可以配置网络规则，仅允许来自特定虚拟网络的应用程序访问存储帐户。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)。 

本文将演示如何使用以下 PowerShell cmdlet 管理这些设置：
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="using-storage-analytics"></a>使用存储分析  

[Azure 存储分析](storage-analytics.md)由[存储分析度量值](/rest/api/storageservices/about-storage-analytics-metrics)和[存储分析日志记录](/rest/api/storageservices/about-storage-analytics-logging)组成。 

存储分析度量值用于收集 Azure 存储帐户的度量值，可用于监视存储帐户的运行情况。 可针对 blob、文件、表和队列启用度量值。

存储分析日志记录在服务器端执行，可用于记录对存储帐户的成功和失败请求的相关详细信息。 使用这些日志，可以查看针对表、队列和 Blob 的读取、写入和删除操作的详细信息，以及请求失败的原因。 日志记录不可用于 Azure 文件。

可以使用 [Azure 门户](https://portal.azure.com)或 PowerShell 配置监视，也可以使用存储客户端库以编程方式配置监视。 

> [!NOTE]
> 可以使用 PowerShell 启用分钟分析。 此功能在门户中不可用。
>

* 若要了解如何使用 PowerShell 启用和查看存储度量值数据，请参阅[启用 Azure 存储度量值和查看度量值数据](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell)。

* 若要了解如何使用 PowerShell 启用和检索存储日志记录数据，请参阅[如何使用 PowerShell 启用存储日志记录](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell)和[查找存储日志记录的日志数据](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data)。

* 有关使用“存储度量值”和“存储日志记录”排查存储问题的详细信息，请参阅[对 Microsoft Azure 存储进行监视、诊断和故障排除](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="managing-the-data-in-the-storage-account"></a>管理存储帐户中的数据

现在，你已了解如何使用 PowerShell 管理存储帐户，下面的文章将介绍如何使用 PowerShell 访问存储帐户中的数据对象。

* [如何使用 PowerShell 管理 blob](../blobs/storage-how-to-use-blobs-powershell.md)
* [如何使用 PowerShell 管理文件](../files/storage-how-to-use-files-powershell.md)
* [如何使用 PowerShell 管理队列](../queues/storage-powershell-how-to-use-queues.md)

<!--## Government Cloud and China Cloud

ROBINROBINROBIN 

To access the Government cloud of the China datacenters, you have to use some special steps. The following article shows how to access these special cloud accounts using PowerShell.

* [How to manage storage accounts in Government Cloud and China](storage-powershell-govt-china.md)
-->

## <a name="next-steps"></a>后续步骤

本操作说明文章介绍了使用管理平面 cmdlet 管理存储帐户的常见操作。 学习如何： 

> [!div class="checklist"]
> * 列出存储器帐户
> * 获取对现有存储帐户的引用
> * 创建存储帐户 
> * 设置存储帐户属性
> * 检索和再生成访问密钥
> * 保护对存储帐户的访问 
> * 启用存储分析

还提供指向多篇其他文章的链接，例如，如何管理数据对象、如何启用存储分析。 下面是一些可供参考的其他相关文章和资源： 
<!--, and how to access storage with PowerShell using the Government Cloud and the China Cloud.
-->

* [Azure 存储控制平面 PowerShell cmdlet](/powershell/module/AzureRM.Storage/)
* [Azure 存储数据平面 PowerShell cmdlet](/powershell/module/azure.storage/)
* [Windows PowerShell Reference](https://msdn.microsoft.com/library/ms714469.aspx)（Windows PowerShell 参考）