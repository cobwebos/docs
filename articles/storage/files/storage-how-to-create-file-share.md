---
title: 创建 Azure 文件共享
titleSuffix: Azure Files
description: 如何使用 Azure 门户、PowerShell 或 Azure CLI 创建 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596860"
---
# <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
要创建 Azure 文件共享，您需要回答有关如何使用它三个问题：

- **Azure 文件共享的性能要求是什么？**  
    Azure 文件提供标准文件共享，这些共享托管在基于硬盘（基于 HDD 的）硬件上，高级文件共享托管在基于固态磁盘（基于 SSD）的硬件上。

- **您需要大小文件共享？**  
    标准文件共享可以跨越多达 100 TiB，但默认情况下未启用此功能;但是，默认情况下，此功能不会启用。如果需要大于 5 TiB 的文件共享，则需要为存储帐户启用大型文件共享功能。 高级文件共享可以跨越多达 100 TiB 没有任何特殊设置，但是高级文件共享是预配的，而不是像标准文件共享一样付费。 这意味着预配文件共享比所需共享大得多，这将增加存储总成本。

- **Azure 文件共享的冗余要求是什么？**  
    标准文件共享提供本地冗余 （LRS）、区域冗余 （ZRS）、地理冗余 （GRS） 或地理区域冗余 （GZRS） 存储，但大型文件共享功能仅支持本地冗余和区域冗余文件共享。 高级文件共享不支持任何形式的异地冗余。

    在大多数提供存储帐户的区域提供本地冗余，在较小的区域子集中提供区域冗余，可以使用高级文件共享。 若要确定高级文件共享目前是否可在你的区域中使用，请参阅 Azure 的[产品的上市区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)页。 有关支持 ZRS 的区域的信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

有关这三个选项的详细信息，请参阅规划[Azure 文件部署](storage-files-planning.md)。

## <a name="prerequisites"></a>先决条件
- 本文假定您已经创建了 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 如果要使用 Azure PowerShell，[请安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果要使用 Azure CLI，[请安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-storage-account"></a>创建存储帐户
Azure 文件共享部署到*存储帐户*中，这些帐户是表示共享存储池的顶级对象。 此存储池可用于部署多个文件共享。 

Azure 支持客户可能具有的不同存储方案的多种类型的存储帐户，但 Azure 文件的存储帐户有两种主要类型。 您需要创建哪种存储帐户类型取决于是要创建标准文件共享还是高级文件共享： 

- **通用版本 2 （GPv2） 存储帐户**：GPv2 存储帐户允许您在基于标准/基于硬盘（基于 HDD）的硬件上部署 Azure 文件共享。 除了存储 Azure 文件共享外，GPv2 存储帐户还可以存储其他存储资源，如 Blob 容器、队列或表。 

- **文件存储存储帐户**：文件存储存储帐户允许您在基于高级/固态磁盘（基于 SSD）的硬件上部署 Azure 文件共享。 文件存储帐户只能用于存储 Azure 文件共享;因此，文件存储帐户只能用于存储 Azure 文件共享。无法在 FileStorage 帐户中部署其他存储资源（blob 容器、队列、表等）。

# <a name="portal"></a>[门户](#tab/azure-portal)
要通过 Azure 门户创建存储帐户，请选择 **" 从仪表板创建资源**"。 在生成的 Azure 应用商店搜索窗口中，搜索**存储帐户**并选择生成的搜索结果。 这将导致存储帐户的概览页;选择 **"创建**"以继续存储帐户创建向导。

![存储帐户快速创建选项的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>基础知识部分
创建存储帐户要完成的第一部分标记为 **"基础知识**"。 它包含创建存储帐户所需的所有字段。 要创建 GPv2 存储帐户，请确保**性能**单选按钮设置为*标准*，并且 **"帐户"类**下拉列表选择存储*V2（通用 v2）。*

!["性能单选"按钮的屏幕截图，其中选择了"标准"屏幕，选择了"已选择存储V2的帐户类型"](media/storage-how-to-create-file-share/create-storage-account-1.png)

要创建文件存储存储帐户，请确保**性能**单选按钮设置为*高级*，并且 **"帐户"类**下拉列表选择到*文件存储*。

![选择"高级"和"选择文件存储的帐户类型"性能单选按钮的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-2.png)

其他基础知识字段独立于存储帐户的选择：
- **订阅**：要部署到的存储帐户的订阅。 
- **资源组**：要部署到的存储帐户的资源组。 您可以创建新资源组或使用现有资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。
- **存储帐户名称**：要创建的存储帐户资源的名称。 此名称必须是全局唯一的，但除此之外，任何您想要的名称都可以。 当您通过 SMB 装载 Azure 文件共享时，存储帐户名称将用作服务器名称。
- **位置**：要部署到的存储帐户的区域。 这可以是与资源组或任何其他可用区域关联的区域。
- **复制**：虽然这被标记为复制，但此字段实际上意味着**冗余**;这是所需的冗余级别：本地冗余 （LRS）、区域冗余 （ZRS）、地理冗余 （GRS） 和地理区域冗余。 此下拉列表还包含读取访问地理冗余 （RA-GRS） 和读取访问地理区域冗余 （RA-GZRS），它们不适用于 Azure 文件共享;在具有这些选择的存储帐户中创建的任何文件共享实际上将分别为异地冗余或地理区域冗余。 根据您所在的区域或选定的存储帐户类型，可能不允许使用某些冗余选项。
- **访问层**：此字段不适用于 Azure 文件，因此您可以选择其中一个单选按钮。

#### <a name="the-networking-blade"></a>网络边栏选项卡
网络部分允许您配置网络选项。 这些设置是创建存储帐户的可选设置，如果需要，可以稍后进行配置。 有关这些选项的详细信息，请参阅[Azure 文件网络注意事项](storage-files-networking-overview.md)。

#### <a name="the-advanced-blade"></a>高级刀片
高级部分包含 Azure 文件共享的几个重要设置：

- **需要安全转移**：此字段指示存储帐户是否需要加密才能与存储帐户通信。 我们建议保持启用状态，但是，如果您需要 SMB 2.1 支持，则必须禁用此功能。 我们建议您禁用加密，以便将存储帐户访问限制到具有服务终结点和/或专用终结点的虚拟网络。
- **大型文件共享**：此字段启用最多 100 TiB 的文件共享的存储帐户。 启用此功能将限制存储帐户仅本地冗余和区域冗余存储选项。 为大型文件共享启用 GPv2 存储帐户后，无法禁用大型文件共享功能。 文件存储存储帐户（高级文件共享的存储帐户）没有此选项，因为所有高级文件共享可以扩展到 100 TiB。 

![应用于 Azure 文件的重要高级设置的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-3.png)

高级选项卡中提供的其他设置（Blob 软删除、Azure 数据湖存储第 2 代的分层命名空间和 Blob 存储的 NFSv3）不适用于 Azure 文件。

#### <a name="tags"></a>Tags
标记是名称/值对，使您能够通过将同一标记应用于多个资源和资源组来对资源进行分类并查看合并计费。 这些是可选的，可以在创建存储帐户后应用。

#### <a name="review--create"></a>查看 + 创建
创建存储帐户的最后一步是选择 **"审阅 + 创建**"选项卡上的 **"创建**"按钮。如果未填充存储帐户的所有必需字段，则此按钮将不可用。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)
要使用 PowerShell 创建存储帐户，我们将使用`New-AzStorageAccount`cmdlet。 这个 cmdlet 有很多选项;仅显示所需的选项。 要了解有关高级选项的详细信息，[`New-AzStorageAccount`请参阅 cmdlet 文档](/powershell/module/az.storage/new-azstorageaccount)。

为了简化存储帐户和后续文件的创建，我们将在变量中存储多个参数。 您可以将变量内容替换为所需的任何值，但请注意，存储帐户名称必须全局唯一。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

要创建能够存储标准 Azure 文件共享的存储帐户，我们将使用以下命令。 该`-SkuName`参数与所需的冗余类型相关;如果需要异地冗余或地理区域冗余存储帐户，还必须删除该`-EnableLargeFileShare`参数。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

要创建能够存储高级 Azure 文件共享的存储帐户，我们将使用以下命令。 请注意，`-SkuName`参数已更改，以包括本地冗`Premium`余 （）`LRS`的两个冗余级别和所需的冗余级别。 参数`-Kind`不是`FileStorage``StorageV2`因为必须在文件存储存储帐户而不是 GPv2 存储帐户中创建高级文件共享。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要使用 Azure CLI 创建存储帐户，我们将使用 az 存储帐户创建命令。 此命令有许多选项;但是，此命令具有许多选项。仅显示所需的选项。 要了解有关高级选项的详细信息，请参阅[`az storage account create`命令文档](/cli/azure/storage/account)。

为了简化存储帐户和后续文件的创建，我们将在变量中存储多个参数。 您可以将变量内容替换为所需的任何值，但请注意，存储帐户名称必须全局唯一。

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

要创建能够存储标准 Azure 文件共享的存储帐户，我们将使用以下命令。 该`--sku`参数与所需的冗余类型相关;如果需要异地冗余或地理区域冗余存储帐户，还必须删除该`--enable-large-file-share`参数。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

要创建能够存储高级 Azure 文件共享的存储帐户，我们将使用以下命令。 请注意，`--sku`参数已更改，以包括本地冗`Premium`余 （）`LRS`的两个冗余级别和所需的冗余级别。 参数`--kind`不是`FileStorage``StorageV2`因为必须在文件存储存储帐户而不是 GPv2 存储帐户中创建高级文件共享。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>创建文件共享
创建存储帐户后，剩下的就是创建文件共享。 无论您使用的是高级文件共享还是标准文件共享，此过程都大致相同。 主要区别是**配额**及其表示的内容。

对于标准文件共享，它是 Azure 文件共享的上限，最终用户无法超越该边界。 标准文件共享配额的主要目的是预算："我不希望此文件共享超过此点"。 如果未指定配额，则标准文件共享可以跨越多达 100 TiB（如果未为存储帐户设置大型文件共享属性，则为 5 TiB）。

对于高级文件共享，配额重载为平均**预配大小**。 预配大小是您要计费的金额，而不考虑实际使用情况。 预配高级文件共享时，需要考虑两个因素：1） 从空间利用率的角度来看，共享的未来增长和 2） 工作负载所需的 IOPS。 每个预配的 GiB 都允许您进行额外的预留和突发 IOPS。 有关如何规划高级文件共享的详细信息，请参阅[预配高级文件共享](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。

# <a name="portal"></a>[门户](#tab/azure-portal)
如果刚刚创建了存储帐户，则可以通过选择 **"转到资源"** 从部署屏幕导航到它。 如果以前已创建存储帐户，则可以通过包含该帐户的资源组导航到该帐户。 进入存储帐户后，选择标记为 **"文件共享"的**磁贴（您还可以通过存储帐户的目录导航到**文件共享**）。

![文件共享磁贴的屏幕截图](media/storage-how-to-create-file-share/create-file-share-1.png)

在文件共享列表中，您应该看到以前在此存储帐户中创建的任何文件共享;因此，应看到以前在此存储帐户中创建的任何文件共享。尚未创建文件共享的空表。 选择 **= 文件共享**以创建新的文件共享。

新的文件共享边栏选项卡应显示在屏幕上。 完成新文件共享边栏选项卡中的字段以创建文件共享：

- **名称**：要创建的文件共享的名称。
- **配额**：标准文件共享的文件共享配额;高级文件共享的文件共享的预配大小。

选择 **"创建**"以完成创建新共享。 请注意，如果存储帐户位于虚拟网络中，则除非客户端也在虚拟网络中，否则将无法成功创建 Azure 文件共享。 您还可以使用 Azure PowerShell `New-AzRmStorageShare` cmdlet 解决此时间点限制。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)
您可以使用[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare)cmdlet 创建 Azure 文件共享。 以下 PowerShell 命令假定您已设置变量`$resourceGroupName`，并且`$storageAccountName`如上述使用 Azure PowerShell 部分创建存储帐户中定义的变量。 

> [!Important]  
> 对于高级文件共享，参数`-QuotaGiB`引用文件共享的预配大小。 文件共享的预配大小是您要计费的金额，无论使用情况如何。 标准文件共享根据使用情况而不是预配大小计费。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
必须先获取存储帐户密钥以授权使用 Azure CLI 创建文件共享。 这可以通过命令[`az storage account keys list`](/cli/azure/storage/account/keys)完成：

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

获得存储帐户密钥后，可以使用[`az storage share create`](/cli/azure/storage/share)命令创建 Azure 文件共享。 

> [!Important]  
> 对于高级文件共享，参数`--quota`引用文件共享的预配大小。 文件共享的预配大小是您要计费的金额，无论使用情况如何。 标准文件共享根据使用情况而不是预配大小计费。

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

如果存储帐户包含在虚拟网络中，并且您从中调用此命令的计算机不是虚拟网络的一部分，则此命令将失败。 您可以通过使用上述 Azure PowerShell `New-AzRmStorageShare` cmdlet 或从属于虚拟网络的计算机（包括通过 VPN 连接）执行 Azure CLI 来解决此时间点限制。

---

> [!Note]  
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="next-steps"></a>后续步骤
- [计划部署 Azure 文件](storage-files-planning.md)或[计划部署 Azure 文件同步](storage-sync-files-planning.md)。 
- [网络概述](storage-files-networking-overview.md)。
- 在[Windows、macOS](storage-how-to-use-files-windows.md)和[macOS](storage-how-to-use-files-mac.md)[Linux](storage-how-to-use-files-linux.md)上连接并装载文件共享。