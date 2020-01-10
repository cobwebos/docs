---
title: 启用和创建大型文件共享-Azure 文件
description: 本文介绍如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a9b545d71f21138c0374cf199ce10dc2dc246afb
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732138"
---
# <a name="enable-and-create-large-file-shares"></a>启用和创建大型文件共享

最初，标准文件共享最多只能扩展到 5 TiB。 现在，对于大型文件共享，它们最多可以扩展到 100 TiB。 可以在现有的文件共享的现有存储帐户上启用此缩放。 默认情况下，高级文件共享最多可扩展到 100 TiB。

## <a name="prerequisites"></a>必备组件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 如果要使用 Azure CLI，请[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果要使用 Azure PowerShell，请[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

目前，只能对启用了大型文件共享的帐户使用 LRS 或 ZRS。 不能使用 GZRS、GRS 或 GRS。
在一个帐户上启用大型文件共享是一种无法恢复的过程。 启用后，你将无法将你的帐户转换为 GZRS、GRS 或 GRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

### <a name="portal"></a>门户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，选择“所有服务”。 
1. 在资源列表中，输入 "**存储帐户**"。 键入时，列表会根据输入的内容进行筛选。 选择“存储帐户”。
1. 在出现的 "**存储帐户**" 窗口中，选择 "**添加**"。
1. 选择要用于创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称。

    ![显示如何在门户中创建资源组的屏幕截图](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 然后，输入存储帐户的名称。 该名称在 Azure 中必须唯一。 该名称的长度必须为3到24个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置，并确保它是[大型文件共享支持的区域之一](storage-files-planning.md#regional-availability)。
1. 将复制设置为**本地冗余存储**或**区域冗余存储**。
1. 将这些字段保留为其默认值：

   |字段  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |性能     |标准         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |访问层     |热         |

1. 选择 "**高级**"，然后选择**大文件共享**右侧的 "**已启用**" 选项按钮。
1. 选择“查看+创建”可查看存储帐户设置并创建帐户。

    ![在 Azure 门户的新存储帐户上使用 "已启用" 选项按钮的屏幕截图](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 选择“创建”。

### <a name="cli"></a>CLI

首先，[安装最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)以便可以启用大型文件共享。

若要创建启用了大文件共享的存储帐户，请使用以下命令。 将 `<yourStorageAccountName>`、`<yourResourceGroup>`和 `<yourDesiredRegion>` 替换为您的信息。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先，[安装最新版本的 PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) ，以便能够启用大型文件共享。

若要创建启用了大文件共享的存储帐户，请使用以下命令。 将 `<yourStorageAccountName>`、`<yourResourceGroup>`和 `<yourDesiredRegion>` 替换为您的信息。

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>在现有帐户上启用大型文件共享

你还可以在现有帐户上启用大型文件共享。 如果启用大型文件共享，则无法转换为 GZRS、GRS 或 GRS。 对此存储帐户启用大文件共享是不可逆的。

### <a name="portal"></a>门户

1. 打开[Azure 门户](https://portal.azure.com)，然后前往要启用大型文件共享的存储帐户。
1. 打开存储帐户并选择 "**配置**"。
1. 选择 "在**大型文件共享**上**启用**"，然后选择 "**保存**"。
1. 选择 "**概述**"，然后选择 "**刷新**"。

![在 Azure 门户中的现有存储帐户上选择 "已启用" 选项按钮](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

你现在已在存储帐户上启用了大型文件共享。 接下来，必须更新现有共享的配额才能利用增加的容量和规模。

如果收到错误消息 "目前还不能使用该帐户的大型文件共享"，则可能是你所在地区正在完成其推出。 如果你对大型文件共享有紧急需求，请联系支持人员。

### <a name="cli"></a>CLI

若要在现有帐户上启用大型文件共享，请使用以下命令。 将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为您的信息。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

若要在现有帐户上启用大型文件共享，请使用以下命令。 将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为您的信息。

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>创建大文件共享

在存储帐户上启用大型文件共享后，可以在该帐户中创建具有更高配额的文件共享。 

### <a name="portal"></a>门户

创建较大的文件共享与创建标准文件共享几乎完全相同。 主要区别在于，最多可将配额设置为 100 TiB。

1. 从存储帐户中，选择 "**文件共享**"。
1. 选择“+ 文件共享”。
1. 输入文件共享的名称。 还可以设置所需的配额大小，最大为 100 TiB。 然后选择“创建”。 

![显示 "名称" 和 "配额" 框的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

若要创建较大的文件共享，请使用以下命令。 将 `<yourStorageAccountName>`、`<yourStorageAccountKey>`和 `<yourFileShareName>` 替换为您的信息。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

若要创建较大的文件共享，请使用以下命令。 将 `<YourStorageAccountName>`、`<YourStorageAccountKey>`和 `<YourStorageAccountFileShareName>` 替换为您的信息。

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>展开现有文件共享

在存储帐户上启用大型文件共享后，还可以将该帐户中的现有文件共享扩展到较高的配额。 

### <a name="portal"></a>门户

1. 从存储帐户中，选择 "**文件共享**"。
1. 右键单击文件共享，然后选择 "**配额**"。
1. 输入所需的新大小，然后选择 **"确定"** 。

![具有现有文件共享配额的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

若要将配额设置为最大大小，请使用以下命令。 将 `<yourStorageAccountName>`、`<yourStorageAccountKey>`和 `<yourFileShareName>` 替换为您的信息。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

若要将配额设置为最大大小，请使用以下命令。 将 `<YourStorageAccountName>`、`<YourStorageAccountKey>`和 `<YourStorageAccountFileShareName>` 替换为您的信息。

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

* [在 Windows 上连接并装载文件共享](storage-how-to-use-files-windows.md)
* [在 Linux 上连接并装载文件共享](storage-how-to-use-files-linux.md)
* [在 macOS 上连接并装载文件共享](storage-how-to-use-files-mac.md)
