---
title: 启用和创建大型文件共享 - Azure 文件
description: 在本文中，您将了解如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537672"
---
# <a name="enable-and-create-large-file-shares"></a>启用并创建大型文件共享

在存储帐户上启用大型文件共享时，文件共享可以扩展到 100 TiB。 您可以在现有文件共享的现有存储帐户上启用此缩放。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 如果要使用 Azure CLI，[请安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果要使用 Azure PowerShell，[请安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

目前，您只能在启用大型文件共享的帐户上使用本地冗余存储 （LRS） 或区域冗余存储 （ZRS）。 不能使用地理区域冗余存储 （GZRS）、异地冗余存储 （GRS） 或读取访问异地冗余存储 （RA-GRS）。
在帐户上启用大型文件共享是一个不可逆的过程。 启用后，您将无法将您的帐户转换为 GZRS、GRS 或 RA-GRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

### <a name="portal"></a>门户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，选择 **"所有服务**"。 
1. 在资源列表中，输入**存储帐户**。 键入时，列表会根据输入的内容进行筛选。 选择**存储帐户**。
1. 在显示的 **"存储帐户**"窗口中，选择"**添加**"。
1. 选择用于创建存储帐户的订阅。
1. 在“资源组”**** 字段下，选择“新建”****。 输入新资源组的名称。

    ![显示如何在门户中创建资源组的屏幕截图](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 然后，输入存储帐户的名称。 该名称在 Azure 中必须唯一。 名称的长度也必须为 3 到 24 个字符，并且只能有数字和小写字母。
1. 选择存储帐户的位置。
1. 将复制设置为**本地冗余存储**或**区域冗余存储**。
1. 将这些字段保留为其默认值：

   |字段  |值  |
   |---------|---------|
   |部署模型     |资源管理器         |
   |性能     |Standard         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |访问层     |热         |

1. 选择 **"高级**"，然后选择 **"大型文件共享**右侧的**启用**"选项按钮。
1. 选择“查看+创建”**** 可查看存储帐户设置并创建帐户。

    ![在 Azure 门户中新存储帐户上使用"已启用"选项按钮的屏幕截图](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 选择“创建”  。

### <a name="cli"></a>CLI

首先，[安装最新版本的 Azure CLI，](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)以便可以启用大型文件共享。

要创建启用大型文件共享的存储帐户，请使用以下命令。 用`<yourStorageAccountName>`您的信息`<yourResourceGroup>`替换`<yourDesiredRegion>`和 。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先，[安装最新版本的 PowerShell，](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)以便您可以启用大型文件共享。

要创建启用大型文件共享的存储帐户，请使用以下命令。 用`<yourStorageAccountName>`您的信息`<yourResourceGroup>`替换`<yourDesiredRegion>`和 。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>在现有帐户上启用大型文件共享

您还可以在现有帐户上启用大型文件共享。 如果启用大型文件共享，将无法转换为 GZRS、GRS 或 RA-GRS。 在此存储帐户上启用大型文件共享是不可逆的。

### <a name="portal"></a>门户

1. 打开[Azure 门户](https://portal.azure.com)，然后转到要启用大型文件共享的存储帐户。
1. 打开存储帐户并选择 **"配置**"。
1. 在**大型文件共享**上选择**启用**，然后选择 **"保存**"。
1. 选择 **"概览"** 并选择 **"刷新**"。

![在 Azure 门户中在现有存储帐户上选择"已启用"选项按钮](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

您现在已启用存储帐户上的大型文件共享。 接下来，必须更新现有共享的配额，以利用增加的容量和规模。

如果您收到错误消息"大型文件共享尚不可用，则您所在的区域可能正在完成其推出。 如果您迫切需要大型文件共享，请联系支持人员。

### <a name="cli"></a>CLI

要在现有帐户上启用大型文件共享，请使用以下命令。 替换`<yourStorageAccountName>`并`<yourResourceGroup>`随您的信息。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

要在现有帐户上启用大型文件共享，请使用以下命令。 替换`<yourStorageAccountName>`并`<yourResourceGroup>`随您的信息。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>创建大型文件共享

在存储帐户上启用大型文件共享后，可以在该帐户中创建具有较高配额的文件共享。 

### <a name="portal"></a>门户

创建大型文件共享与创建标准文件共享几乎相同。 主要区别是，您可以设置最多 100 TiB 的配额。

1. 从存储帐户中，选择 **"文件共享**"。
1. 选择“+ 文件共享”。****
1. 输入文件共享的名称。 您还可以设置您希望的配额大小，最多 100 TiB。 然后选择“创建”  。 

![显示名称和配额框的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

要创建大型文件共享，请使用以下命令。 用`<yourStorageAccountName>`您的信息`<yourStorageAccountKey>`替换`<yourFileShareName>`和 。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

要创建大型文件共享，请使用以下命令。 用`<YourStorageAccountName>`您的信息`<YourStorageAccountKey>`替换`<YourStorageAccountFileShareName>`和 。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>扩展现有文件共享

在存储帐户上启用大型文件共享后，还可以将该帐户中的现有文件共享扩展到更高的配额。 

### <a name="portal"></a>门户

1. 从存储帐户中，选择 **"文件共享**"。
1. 右键单击文件共享，然后选择 **"配额**"。
1. 输入所需的新大小，然后选择 **"确定**"。

![具有现有文件共享配额的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

要将配额设置为最大大小，请使用以下命令。 用`<yourStorageAccountName>`您的信息`<yourStorageAccountKey>`替换`<yourFileShareName>`和 。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

要将配额设置为最大大小，请使用以下命令。 用`<YourStorageAccountName>`您的信息`<YourStorageAccountKey>`替换`<YourStorageAccountFileShareName>`和 。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>后续步骤

* [在 Windows 上连接并装载文件共享](storage-how-to-use-files-windows.md)
* [在 Linux 上连接并装载文件共享](storage-how-to-use-files-linux.md)
* [在 macOS 上连接并装载文件共享](storage-how-to-use-files-mac.md)
