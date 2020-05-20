---
title: 快速入门：使用 Azure CLI 管理 Azure 文件共享
description: 通过本快速入门了解如何使用 Azure CLI 来管理 Azure 文件。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95d7abca27ec9db46a72140bc8a61b2841c63fcb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77598552"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>快速入门：使用 Azure CLI 创建和管理 Azure 文件共享
本指南介绍通过 Azure CLI 来使用 [Azure 文件共享](storage-files-introduction.md)的基本知识。 Azure 文件共享与其他文件共享一样，只不过是存储在云中并由 Azure 平台提供支持。 Azure 文件共享支持行业标准 SMB 协议，可以跨多个计算机、应用程序和实例进行文件共享。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，则必须运行 Azure CLI 2.0.4 或更高版本才能执行本文中的步骤。 若要查找 Azure CLI 版本，请运行 **az --version**。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

默认情况下，Azure CLI 命令返回 JavaScript 对象表示法 (JSON)。 JSON 是通过 REST API 发送和接收消息的标准方式。 为了便于处理 JSON 响应，本文中的某些示例使用基于 Azure CLI 命令的 *query* 参数。 该参数使用 [JMESPath 查询语言](http://jmespath.org/)来分析 JSON。 若要详细了解如何按照 JMESPath 查询语言的规范来使用 Azure CLI 命令的结果，请参阅 [JMESPath tutorial](http://jmespath.org/tutorial.html)（JMESPath 教程）。

## <a name="create-a-resource-group"></a>创建资源组
资源组是在其中部署和管理 Azure 资源的逻辑容器。 如果还没有 Azure 资源组，可以使用 [az group create](/cli/azure/group) 命令创建一个。 

以下示例在“美国西部 2”位置创建名为“myResourceGroup”的资源组：

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>创建存储帐户
存储帐户是一个存储共享池，在其中可以部署 Azure 文件共享或其他存储资源，例如 Blob 或队列。 一个存储帐户可以包含无数个文件共享。 一个共享可以存储无数个文件，直至达到存储帐户的容量限制为止。

以下示例使用 [az storage account create](/cli/azure/storage/account) 命令创建一个存储帐户。 存储帐户名称必须唯一，因此请使用 `$RANDOM` 将一个数字追加到名称末尾，使之变得唯一。

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> 超过 5 TiB（每个共享最多100 TiB）的共享仅适用于本地冗余 (LRS) 和区域冗余 (ZRS) 存储帐户。 若要创建异地冗余 (GRS) 或异地区域冗余 (GZRS) 存储帐户，请删除 `--enable-large-file-share` 参数。

### <a name="get-the-storage-account-key"></a>获取存储帐户密钥
存储帐户密钥控制对存储帐户中资源的访问。 这些密钥是在创建存储帐户时自动创建的。 可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令获取存储帐户的存储帐户密钥： 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
现在可以创建第一个 Azure 文件共享了。 请使用 [az storage share create](/cli/azure/storage/share) 命令创建文件共享。 以下示例创建名为 *myshare* 的 Azure 文件共享： 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

共享名称只能包含小写字母、数字和单个连字符（但不能以连字符开头）。 有关为文件共享和文件命名的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="use-your-azure-file-share"></a>使用 Azure 文件共享
Azure 文件提供两种在 Azure 文件共享中使用文件和文件夹的方法：行业标准[服务器消息块 (SMB) 协议](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)和[文件 REST 协议](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)。 

若要通过 SMB 装载文件共享，请参阅下述基于 OS 的文档：
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>将 Azure 文件共享与文件 REST 协议配合使用 
可以直接使用文件 REST 协议（手动进行 REST HTTP 调用），但最常见的使用文件 REST 协议的方式是使用 Azure CLI、[Azure PowerShell 模块](storage-how-to-use-files-powershell.md)或 Azure 存储 SDK，所有这些方式都可以在所选脚本/编程语言中为文件 REST 协议提供很好的包装器。  

我们预计，在使用 Azure 文件时，大多数情况下需要通过 SMB 协议来使用 Azure 文件共享，因为这样可以使用那些预期可以使用的现有应用程序和工具，但某些情况下，使用文件 REST API 比使用 SMB 更具优势，例如：

- 需要通过 Azure Bash Cloud Shell（不能通过 SMB 来装载文件共享）来浏览文件共享。
- 需利用无服务器资源，例如 [Azure Functions](../../azure-functions/functions-overview.md)。 
- 你将创建将与许多 Azure 文件共享进行交互的增值服务，例如执行备份或防病毒扫描。

以下示例介绍如何使用 Azure CLI 通过文件 REST 协议来操作 Azure 文件共享。 

### <a name="create-a-directory"></a>创建目录
若要在 Azure 文件共享的根目录中创建名为 *myDirectory* 的新目录，请使用 [`az storage directory create`](/cli/azure/storage/directory) 命令：

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>上传文件
若要演示如何使用 [`az storage file upload`](/cli/azure/storage/file) 命令来上传文件，请首先在 Cloud Shell 暂存驱动器上创建要上传的文件。 以下示例创建并上传该文件：

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

如果在本地运行 Azure CLI，请将 `~/clouddrive` 替换为计算机上的现有路径。

上传文件以后，即可使用 [`az storage file list`](/cli/azure/storage/file) 命令，确保文件已上传到 Azure 文件共享：

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>下载文件
可以使用 [`az storage file download`](/cli/azure/storage/file) 命令下载已上传到 Cloud Shell 暂存驱动器的文件的副本：

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>复制文件
一项常见的任务是将文件从一个文件共享复制到另一个文件共享。 为了演示此功能，请创建一个新的共享。 请使用 [az storage file copy](/cli/azure/storage/file/copy) 命令将上传的文件复制到该新共享： 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

现在，如果列出新共享中的文件，则应看到已复制的文件：

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

虽然 `az storage file copy start` 命令可以方便地用于 Azure 文件共享之间的文件移动，但对于迁移和更大的数据移动，我们建议在 macOS 和 Linux 上使用 `rsync`，在 Windows 上使用 `robocopy`。 `rsync` 和 `robocopy` 使用 SMB 而不是 FileREST API 来执行数据移动。

## <a name="create-and-manage-share-snapshots"></a>创建和管理共享快照
可以通过 Azure 文件共享执行的另一项有用的任务是创建共享快照。 快照保存 Azure 文件共享在某个时间点的副本。 共享快照类似于你可能已经熟悉的某些操作系统技术：

- 适用于 Linux 系统的[逻辑卷管理器 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照。
- 适用于 macOS 的 [Apple 文件系统 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照。
- 适用于 Windows 文件系统（例如 NTFS 和 ReFS）的[卷影复制服务 (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)。
 
可以使用 [`az storage share snapshot`](/cli/azure/storage/share) 命令创建共享快照：

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>浏览共享快照内容
可以浏览共享快照的内容，只需将变量 `$snapshot` 中捕获的共享快照的时间戳传递给 `az storage file list` 命令即可：

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>列出共享快照
若要查看为共享生成的快照的列表，请使用以下命令：

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>从共享快照还原
可以使用此前用过的 `az storage file copy start` 命令还原某个文件。 首先，请删除已上传的 SampleUpload.txt 文件，这样才能将其从快照还原：

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>删除共享快照
可以使用 [`az storage share delete`](/cli/azure/storage/share) 命令删除共享快照。 所使用的变量包含对 `--snapshot` 参数的 `$SNAPSHOT` 引用：

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>清理资源
完成后，可以使用 [`az group delete`](/cli/azure/group) 命令删除资源组和所有相关的资源： 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

也可逐一删除资源。
- 若要删除为本文创建的 Azure 文件共享，请执行以下命令：

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- 若要删除存储帐户本身，请执行以下命令 （这会隐式删除已创建的 Azure 文件共享，以及任何其他可能已创建的存储资源，例如 Azure Blob 存储容器）：

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [什么是 Azure 文件？](storage-files-introduction.md)