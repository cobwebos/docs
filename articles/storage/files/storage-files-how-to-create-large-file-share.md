---
title: 启用和创建大型文件共享 - Azure 文件存储
description: 本文介绍如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: add2805d9a360d3d9cd45ab54f476a6852fb7bd5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858576"
---
# <a name="enable-and-create-large-file-shares"></a>启用和创建大型文件共享

在存储帐户中启用大型文件共享时，文件共享最大可以扩展到 100 TiB。 可以在现有存储帐户中为现有的文件共享启用此缩放。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 如果你打算使用 Azure CLI，请[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果你打算使用 Azure PowerShell，请[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

现在，只能在启用了大文件共享的帐户上使用本地冗余存储（LRS）或区域冗余存储（ZRS）。 不能使用异地冗余存储（GZRS）、异地冗余存储（GRS）、读取访问异地冗余存储（RA-GRS）或读取访问地域冗余存储（RA-GZRS）。

在帐户中启用大型文件共享是一个不可逆的过程。 启用后，你将无法将你的帐户转换为 GZRS、GRS、GRS 或 GZRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

### <a name="portal"></a>门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，选择“所有服务”。  
1. 在资源列表中输入“存储帐户”。  键入时，列表会根据输入的内容进行筛选。 选择“存储帐户”  。
1. 在显示的“存储帐户”窗口中，选择“添加”。  
1. 选择用于创建存储帐户的订阅。
1. 在“资源组”  字段下，选择“新建”  。 输入新资源组的名称。

    ![显示如何在门户中创建资源组的屏幕截图](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 然后，输入存储帐户的名称。 该名称在 Azure 中必须唯一。 该名称的长度必须是 3 到 24 个字符，只能包含数字和小写字母。
1. 选择存储帐户的位置。
1. 将复制设置为**本地冗余存储**或**区域冗余存储**。
1. 将以下字段保留其默认值：

   |字段  |Value  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |性能     |标准         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |访问层     |热         |

1. 选择“高级”，然后选择“大型文件共享”右侧的“已启用”选项按钮。   
1. 选择“查看+创建”  可查看存储帐户设置并创建帐户。

    ![Azure 门户中新存储帐户对应的“已启用”选项按钮的屏幕截图](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 选择“创建”  。

### <a name="cli"></a>CLI

首先[安装最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，以便可以启用大型文件共享。

若要创建启用了大文件共享的存储帐户，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourResourceGroup>` 和 `<yourDesiredRegion>` 替换为自己的信息。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先[安装最新版本的 PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)，以便可以启用大型文件共享。

若要创建启用了大文件共享的存储帐户，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourResourceGroup>` 和 `<yourDesiredRegion>` 替换为自己的信息。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>在现有帐户中启用大型文件共享

也可以在现有帐户中启用大型文件共享。 如果启用大型文件共享，则无法转换为 GZRS、GRS、GRS 或 GZRS。 在此存储帐户中启用大型文件共享的操作不可逆。

### <a name="portal"></a>门户

1. 打开 [Azure 门户](https://portal.azure.com)，转到要在其中启用大型文件共享的存储帐户。
1. 打开存储帐户并选择“配置”。 
1. 选择“大型文件共享”对应的“已启用”，然后选择“保存”。   
1. 依次选择“概述”、“刷新”。  

![在 Azure 门户中选择现有存储帐户对应的“已启用”选项按钮](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

现已在存储帐户中启用大型文件共享。 接下来，必须更新现有共享的配额才能利用提高的容量和规模。

如果收到错误消息“大型文件共享尚不适用于该帐户”，原因可能是你所在的区域正在推出中。 如果你迫切需要大型文件共享，请与支持人员联系。

### <a name="cli"></a>CLI

若要在现有帐户中启用大型文件共享，请使用以下命令。 请将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为自己的信息。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

若要在现有帐户中启用大型文件共享，请使用以下命令。 请将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为自己的信息。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>创建大型文件共享

在存储帐户中启用大型文件共享后，可以在该帐户中创建配额更高的文件共享。 

### <a name="portal"></a>门户

创建大型文件共享的过程与创建标准文件共享几乎完全相同。 主要差别在于，配额最高可以设置为 100 TiB。

1. 在存储帐户中选择“文件共享”。 
1. 选择“+ 文件共享”。 
1. 输入文件共享的名称。 也可以设置所需的配额大小，不超过 100 TiB 即可。 然后选择“创建”  。 

![显示“名称”和“配额”框的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

若要创建大型文件共享，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourStorageAccountKey>` 和 `<yourFileShareName>` 替换为自己的信息。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

若要创建大型文件共享，请使用以下命令。 请将 `<YourStorageAccountName>`、`<YourStorageAccountKey>` 和 `<YourStorageAccountFileShareName>` 替换为自己的信息。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>扩展现有的文件共享

在存储帐户中启用大型文件共享后，还可以将该帐户中的现有文件共享扩展到更高的配额。 

### <a name="portal"></a>门户

1. 在存储帐户中选择“文件共享”。 
1. 右键单击文件共享并选择“配额”。 
1. 输入所需的新大小，然后选择“确定”。 

![显示了现有文件共享配额的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

若要将配额设置为最大大小，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourStorageAccountKey>` 和 `<yourFileShareName>` 替换为自己的信息。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

若要将配额设置为最大大小，请使用以下命令。 请将 `<YourStorageAccountName>`、`<YourStorageAccountKey>` 和 `<YourStorageAccountFileShareName>` 替换为自己的信息。

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
