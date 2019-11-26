---
title: 启用和创建大型文件共享-Azure 文件
description: 本文介绍如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422728"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>如何启用和创建大型文件共享

最初，标准文件共享最多只能扩展到5个 TiB，而对于大型文件共享，它们最多可以扩展到 100 TiB。 默认情况下，高级文件共享最多可扩展到 100 TiB。 

若要使用标准文件共享来扩展到 100 TiB，必须启用存储帐户才能使用大型文件共享。 可以启用现有帐户，也可以创建新帐户以使用大型文件共享。

## <a name="prerequisites"></a>先决条件

- 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
- 如果要使用 Azure CLI，请确保[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果要使用 Azure PowerShell，请确保[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

启用了大文件共享的帐户支持 LRS 或 ZRS。 目前，启用了大型文件共享的帐户不支持 GZRS、GRS 或 GRS。 在一个帐户上启用大型文件共享是一种不可逆的过程，一旦启用，你的帐户将无法转换为 GZRS、GRS 或 GRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

### <a name="portal"></a>门户

登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“存储帐户”。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”。
1. 在显示的“存储帐户”窗口中，选择“添加”。
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称，如下图中所示。

    ![显示如何在门户中创建资源组的屏幕截图](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置，确保它是[LFS 支持的区域之一](storage-files-planning.md#regional-availability)。
1. 将复制设置为**本地冗余存储**或**区域冗余存储**。
1. 将这些字段设置为其默认值：

   |字段  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |性能     |标准         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |访问层     |热         |

1. 选择 "**高级**" 并选择 "为**大文件共享** **启用**"。
1. 选择“查看+创建”可查看存储帐户设置并创建帐户。

    ![large-file-shares-advanced-enable .png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 选择“创建”。


### <a name="cli"></a>CLI

首先，请确保[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，以便可以启用此功能。

若要创建启用了大文件共享的存储帐户，请将 `<yourStorageAccountName>`、`<yourResourceGroup>`和 `<yourDesiredRegion>` 替换为你的值，然后使用以下命令：

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先，请确保[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)，以便可以启用此功能。

若要创建启用了大文件共享的存储帐户，请将 `<yourStorageAccountName>`、`<yourResourceGroup>`和 `<yourDesiredRegion>` 替换为你的值，然后使用以下命令：

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>对现有帐户启用

### <a name="portal"></a>门户

你还可以在现有帐户上启用大型文件共享。 如果执行此操作，则该帐户将无法再转换为 GZRS、GRS 或 GRS。 此帐户不可逆。

1. 打开[Azure 门户](https://portal.azure.com)，导航到要在其上启用大型文件共享的存储帐户。
1. 打开存储帐户并选择 "**配置**"。
1. 在**大文件共享**上选择 "**启用**"，然后选择 "保存"。
1. 选择 "**概述**"，然后选择 "**刷新**"。

![enable-large-file-shares-on-existing .png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

你现在已在存储帐户上启用了大型文件共享。

如果收到以下错误： "目前还不能使用该帐户的大型文件共享"。 您可以等待一段时间，因为您的区域可能在完成其推出过程中，或者，如果您有紧急需求，请联系支持人员。

### <a name="cli"></a>CLI

可以在现有帐户上启用大型文件共享。 如果执行此操作，则该帐户将无法再转换为 GZRS、GRS 或 GRS。 此帐户不可逆。

在以下命令中替换 `<yourStorageAccountName>` 和 `<yourResourceGroup>`，然后使用它在现有帐户上启用大型文件共享：

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

可以在现有帐户上启用大型文件共享。 如果执行此操作，则该帐户将无法再转换为 GZRS、GRS 或 GRS。 此帐户不可逆。

在以下命令中替换 `<yourStorageAccountName>` 和 `<yourResourceGroup>`，然后使用它在现有帐户上启用大型文件共享：

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>创建大文件共享

### <a name="portal"></a>门户

创建较大的文件共享与创建标准文件共享几乎完全相同。 主要区别在于，最多可将配额设置为 100 TiB。

1. 从存储帐户中，选择 "**文件共享**"。
1. 选择“+ 文件共享”。
1. 输入文件共享的名称和（可选）所需的配额大小，最多 100 TiB，然后选择 "**创建**"。 

![large-file-shares-create-share .png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

在存储帐户上启用大型文件共享后，可以在该帐户中创建具有更高配额的文件共享。 将以下命令中的 `<yourStorageAccountName>`、`<yourStorageAccountKey>`和 `<yourFileShareName>` 替换为你的值，然后可以使用它来创建大型文件共享：

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

在存储帐户上启用大型文件共享后，可以在该帐户中创建具有更高配额的文件共享。 将以下命令中的 `<YourStorageAccountName>`、`<YourStorageAccountKey>`和 `<YourStorageAccountFileShareName>` 替换为你的值，然后可以使用它来创建大型文件共享：

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>展开现有文件共享

### <a name="portal"></a>门户

在存储帐户上启用大型文件共享后，可以将现有共享扩展到较高的配额。

1. 从存储帐户中，选择 "**文件共享**"。
1. 右键单击文件共享，然后选择 "**配额**"。
1. 输入所需的新大小，然后选择 **"确定"** 。

![update-large-file-share-quota .png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

在存储帐户上启用大型文件共享后，可以将该帐户中的现有文件共享扩展到更高的配额。 将以下命令中的 `<yourStorageAccountName>`、`<yourStorageAccountKey>`和 `<yourFileShareName>` 替换为你的值，然后可以使用它将配额设置为最大大小：

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

在存储帐户上启用大型文件共享后，可以将该帐户中的现有文件共享扩展到更高的配额。 将以下命令中的 `<YourStorageAccountName>`、`<YourStorageAccountKey>`和 `<YourStorageAccountFileShareName>` 替换为你的值，然后可以使用它将配额设置为最大大小：

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>后续步骤

* [连接并装载文件共享 - Windows](storage-how-to-use-files-windows.md)
* [连接并装载文件共享 - Linux](../storage-how-to-use-files-linux.md)
* [连接并装载文件共享 - macOS](storage-how-to-use-files-mac.md)