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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596860"
---
# <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
若要创建 Azure 文件共享，需要回答以下有关如何使用它的三个问题：

- **Azure 文件共享的性能要求有哪些？**  
    Azure 文件提供了标准文件共享，它们托管在基于磁盘的基于磁盘（基于 HDD）的硬件上，以及在基于磁盘的基于磁盘（基于 SSD 的）硬件上托管的高级文件共享。

- **需要哪种大小的文件共享？**  
    标准文件共享最多可跨越 100 TiB，但默认情况下不启用此功能;如果需要的文件共享大于 5 TiB，则需要为存储帐户启用大文件共享功能。 高级文件共享最多可跨越 100 TiB，而无需任何特殊设置，但会预配高级文件共享，而不是像标准文件共享一样付费。 这意味着，预配文件共享比你需要的要大得多，会增加存储的总成本。

- **Azure 文件共享的冗余要求是什么？**  
    标准文件共享提供本地冗余（LRS）、区域冗余（ZRS）、异地冗余（GRS）或地理区域冗余（GZRS）存储，但是，仅在本地冗余和区域冗余文件共享上支持大型文件共享功能。 高级文件共享不支持任何形式的异地冗余。

    在大多数区域中提供存储帐户和区域冗余的高级文件共享都可用于较小的区域子集。 若要确定高级文件共享当前是否在你的区域中可用，请参阅 Azure 的 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)" 页。 有关支持 ZRS 的区域的信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

有关这三个选项的详细信息，请参阅[规划 Azure 文件部署](storage-files-planning.md)。

## <a name="prerequisites"></a>必备条件
- 本文假定你已创建 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 如果要使用 Azure PowerShell，请[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果要使用 Azure CLI，请[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-storage-account"></a>创建存储帐户
Azure 文件共享部署到*存储帐户*，这些帐户是代表存储共享池的顶级对象。 此存储池可用于部署多个文件共享。 

对于不同的存储方案，azure 支持多种类型的存储帐户，但对于 Azure 文件，有两种主要类型的存储帐户。 需要创建的存储帐户类型取决于你是要创建标准文件共享还是高级文件共享： 

- **常规用途版本2（GPv2）存储帐户**： GPv2 存储帐户允许你将 Azure 文件共享部署在基于标准/硬盘（基于 HDD）的硬件上。 除了存储 Azure 文件共享以外，GPv2 存储帐户还可以存储其他存储资源，例如 blob 容器、队列或表。 

- **FileStorage 存储帐户**： FileStorage 存储帐户可让你在基于磁盘的基于磁盘（基于 SSD）的硬件上部署 Azure 文件共享。 FileStorage 帐户只能用于存储 Azure 文件共享;其他存储资源（blob 容器、队列、表等）都不能部署在 FileStorage 帐户中。

# <a name="portal"></a>[门户](#tab/azure-portal)
若要通过 Azure 门户创建存储帐户，请从仪表板中选择 " **+ 创建资源**"。 在生成的 Azure Marketplace 搜索窗口中，搜索 "**存储帐户**" 并选择生成的搜索结果。 这将导致存储帐户的 "概述" 页;选择 "**创建**" 以继续执行存储帐户创建向导。

![浏览器中的存储帐户 "快速创建" 选项的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>"基本信息" 部分
创建存储帐户的第一个部分标记为 "**基本**"。 这包含创建存储帐户所需的所有字段。 若要创建 GPv2 存储帐户，请确保 "**性能**" 单选按钮设置为 "*标准*"，并选择 "**帐户类型**" 下拉列表中的 " *StorageV2" （常规用途 v2）* 。

![选择了 "标准" 的 "性能" 单选按钮和 "StorageV2" 选项的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-1.png)

若要创建 FileStorage 存储帐户，请确保 "**性能**" 单选按钮设置为 "*高级*"，并选择 "**帐户类型**" 下拉列表 " *FileStorage*"。

![选择了 "高级" 的 "性能" 单选按钮和 "FileStorage" 的帐户类型的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-2.png)

其他基本信息字段独立于所选的存储帐户：
- **订阅**：要部署到的存储帐户的订阅。 
- **资源组**：要部署到的存储帐户的资源组。 你可以创建新的资源组或使用现有的资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。
- **存储帐户名称**：要创建的存储帐户资源的名称。 此名称必须是全局唯一的，但也可以是所需的任何名称。 如果通过 SMB 装载 Azure 文件共享，则存储帐户名称将用作服务器名称。
- **位置**：要将存储帐户部署到的区域。 这可以是与资源组相关联的区域，也可以是任何其他可用区域。
- **复制**：尽管此字段标记为 "复制"，但此字段实际上表示**冗余**;这是所需的冗余级别：本地冗余（LRS）、区域冗余（ZRS）、异地冗余（GRS）和地域冗余。 此下拉列表还包含读取访问地域冗余（GRS）和读取访问地域冗余（RA-GZRS），这不适用于 Azure 文件共享;所选存储帐户中创建的任何文件共享都将分别为异地冗余或地域冗余。 根据你所在的区域或所选的存储帐户类型，可能无法使用某些冗余选项。
- **访问层**：此字段不适用于 Azure 文件，因此你可以选择任一单选按钮。

#### <a name="the-networking-blade"></a>网络边栏选项卡
"网络" 部分用于配置网络选项。 对于创建存储帐户，这些设置是可选的，并且可以在以后根据需要进行配置。 有关这些选项的详细信息，请参阅[Azure 文件网络注意事项](storage-files-networking-overview.md)。

#### <a name="the-advanced-blade"></a>高级边栏选项卡
"高级" 部分包含 Azure 文件共享的几个重要设置：

- **需要安全传输**：此字段指示存储帐户是否需要在传输过程中进行加密以与存储帐户通信。 建议启用此功能，但是，如果需要 SMB 2.1 支持，则必须禁用此功能。 如果禁用加密，则可以使用服务终结点和/或专用终结点限制存储帐户对虚拟网络的访问。
- **大文件共享**：此字段为最多为 100 TiB 的文件共享启用存储帐户。 启用此功能会将存储帐户限制为仅本地冗余存储和区域冗余存储选项。 为大文件共享启用了 GPv2 存储帐户后，将无法禁用大文件共享功能。 FileStorage 存储帐户（高级文件共享的存储帐户）没有此选项，因为所有高级文件共享最多可以扩展到 100 TiB。 

![适用于 Azure 文件的重要高级设置的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-3.png)

"高级" 选项卡中提供的其他设置（"blob 软删除"、"Azure Data Lake 存储第2层的分层命名空间" 和 "blob 存储的 NFSv3"）不适用于 Azure 文件。

#### <a name="tags"></a>Tags
标记是名称/值对，通过将相同的标记应用到多个资源和资源组，可以对资源进行分类并查看合并的帐单。 这些是可选的，可以在创建存储帐户后应用。

#### <a name="review--create"></a>查看 + 创建
创建存储帐户的最后一个步骤是在 "查看" 和 "**创建**" 选项卡上选择 "**创建**" 按钮。如果未填写存储帐户的所有必需字段，则此按钮将不可用。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 PowerShell 创建存储帐户，我们将使用 `New-AzStorageAccount` cmdlet。 此 cmdlet 有许多选项;只显示所需的选项。 若要了解有关高级选项的详细信息，请参阅[`New-AzStorageAccount` cmdlet 文档](/powershell/module/az.storage/new-azstorageaccount)。

为了简化存储帐户和后续文件共享的创建，我们将在变量中存储多个参数。 您可以将变量内容替换为您需要的任何值，但是请注意，存储帐户名称必须是全局唯一的。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

若要创建能够存储标准 Azure 文件共享的存储帐户，请使用以下命令。 `-SkuName` 参数与所需的冗余类型相关;如果需要异地冗余或地域冗余存储帐户，则还必须删除 `-EnableLargeFileShare` 参数。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

若要创建能够存储高级 Azure 文件共享的存储帐户，请使用以下命令。 请注意，`-SkuName` 参数已更改为同时包含 `Premium` 和本地冗余（`LRS`）的所需冗余级别。 `-Kind` 参数 `FileStorage` 而不是 `StorageV2`，因为必须在 FileStorage 存储帐户而不是 GPv2 存储帐户中创建高级文件共享。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 创建存储帐户，我们将使用 az storage account create 命令。 此命令有很多选项;只显示所需的选项。 若要了解有关高级选项的详细信息，请参阅[`az storage account create` 命令文档](/cli/azure/storage/account)。

为了简化存储帐户和后续文件共享的创建，我们将在变量中存储多个参数。 您可以将变量内容替换为您需要的任何值，但是请注意，存储帐户名称必须是全局唯一的。

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

若要创建能够存储标准 Azure 文件共享的存储帐户，请使用以下命令。 `--sku` 参数与所需的冗余类型相关;如果需要异地冗余或地域冗余存储帐户，则还必须删除 `--enable-large-file-share` 参数。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

若要创建能够存储高级 Azure 文件共享的存储帐户，请使用以下命令。 请注意，`--sku` 参数已更改为同时包含 `Premium` 和本地冗余（`LRS`）的所需冗余级别。 `--kind` 参数 `FileStorage` 而不是 `StorageV2`，因为必须在 FileStorage 存储帐户而不是 GPv2 存储帐户中创建高级文件共享。

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
创建存储帐户后，剩下的就是创建文件共享。 无论你使用的是高级文件共享还是标准文件共享，此过程都是相同的。 主要区别在于**配额**及其表示的内容。

对于标准文件共享，它是 Azure 文件共享的上限，最终用户不能使用它。 标准文件共享的配额的主要用途是预算： "我不希望此文件共享在此时间之外增长"。 如果未指定配额，则标准文件共享最多可跨越 100 TiB （如果没有为存储帐户设置 "大文件共享" 属性，则为 5 TiB）。

对于高级文件共享，配额会重载为平均**预配大小**。 预配的大小为你将为你计费，而不考虑实际使用情况。 当您设置高级文件共享时，您需要考虑两个因素：1）从空间利用率角度来看，该份额的未来增长，以及2）您的工作负荷所需的 IOPS。 每个预配的 GiB 都可以提供额外的保留和突发 IOPS。 有关如何规划高级文件共享的详细信息，请参阅[预配高级文件](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)共享。

# <a name="portal"></a>[门户](#tab/azure-portal)
如果你刚刚创建了存储帐户，可以通过选择 "**转到资源**" 从部署屏幕导航到它。 如果你以前创建了存储帐户，则可以通过包含它的资源组导航到该存储帐户。 在存储帐户中，选择标记为 "**文件共享**" 的磁贴（还可以通过存储帐户的目录导航到 "**文件共享**"）。

![文件共享磁贴的屏幕截图](media/storage-how-to-create-file-share/create-file-share-1.png)

在文件共享列表中，应会看到以前在此存储帐户中创建的任何文件共享;如果尚未创建任何文件共享，则为空表。 选择 " **+ 文件共享**"，创建新的文件共享。

新的 "文件共享" 边栏选项卡应出现在屏幕上。 完成 "新建文件共享" 边栏选项卡中的字段以创建文件共享：

- **Name**：要创建的文件共享的名称。
- **配额**：标准文件共享的文件共享配额;高级文件共享的文件共享的预配大小。

选择 "**创建**" 以完成创建新共享。 请注意，如果你的存储帐户位于虚拟网络中，则你将无法成功创建 Azure 文件共享，除非你的客户端也在虚拟网络中。 你还可以使用 Azure PowerShell `New-AzRmStorageShare` cmdlet 来解决此时间点限制。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
可以通过[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) Cmdlet 创建 Azure 文件共享。 下面的 PowerShell 命令假设你已设置了 "使用 Azure PowerShell 创建存储帐户" 部分中定义的变量 `$resourceGroupName` 和 `$storageAccountName`。 

> [!Important]  
> 对于高级文件共享，`-QuotaGiB` 参数指的是文件共享的预配大小。 文件共享的预配大小是你将向你收费，而不考虑使用情况。 标准文件共享按使用情况计费，而不是按预配大小计费。

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
在使用 Azure CLI 创建 Azure 文件共享之前，你必须获取用于授权文件共享创建操作的存储帐户密钥。 可以通过[`az storage account keys list`](/cli/azure/storage/account/keys)命令完成此操作：

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

拥有存储帐户密钥后，可以使用[`az storage share create`](/cli/azure/storage/share)命令创建 Azure 文件共享。 

> [!Important]  
> 对于高级文件共享，`--quota` 参数指的是文件共享的预配大小。 文件共享的预配大小是你将向你收费，而不考虑使用情况。 标准文件共享按使用情况计费，而不是按预配大小计费。

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

如果存储帐户包含在一个虚拟网络中，而你从中调用此命令的计算机不是虚拟网络的一部分，则此命令将失败。 你可以通过使用上面所述的 Azure PowerShell `New-AzRmStorageShare` cmdlet 来解决这一时间点的限制，或者从作为虚拟网络一部分的计算机（包括通过 VPN 连接）中执行 Azure CLI。

---

> [!Note]  
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件部署](storage-files-planning.md)或[计划部署 Azure 文件同步](storage-sync-files-planning.md)。 
- [网络概述](storage-files-networking-overview.md)。
- 连接并在[Windows](storage-how-to-use-files-windows.md)、 [macOS](storage-how-to-use-files-mac.md)和[Linux](storage-how-to-use-files-linux.md)上装载文件共享。