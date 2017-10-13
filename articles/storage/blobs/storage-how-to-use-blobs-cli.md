---
title: "通过 Azure CLI 对 Azure Blob 存储（对象存储）执行操作 | Microsoft Docs"
description: "了解如何在 Azure Blob 存储中上传和下载 blob，以及如何构造共享访问签名 (SAS) 来管理对存储帐户中的 blob 的访问。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>通过 Azure CLI 对 Blob 存储执行操作

Azure Blob 存储是用于存储大量非结构化对象数据（例如文本或二进制数据）的服务，这些数据可通过 HTTP 或 HTTPS 从世界各地进行访问。 本教程介绍 Azure Blob 存储中的基本操作，如上传、下载和删除 Blob。 学习如何：

> [!div class="checklist"]
> * 创建容器
> * 将文件 (blob) 上传到容器中
> * 列出容器中的 Blob
> * 从容器下载 Blob
> * 在存储帐户之间复制 blob
> * 删除 Blob
> * 显示和修改 blob 属性和元数据
> * 使用共享访问签名 (SAS) 管理安全性

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>创建容器

容器类似于计算机上的目录，允许在容器中组织成组的 Blob，就好比在目录中组织文件。 一个存储帐户可以包含任意数目的容器。 一个容器中最多可以存储 500 TB 的 blob 数据，这是一个存储帐户可以存储的最大数据量。

可以使用 [az storage container create](/cli/azure/storage/container#create) 命令创建用于存储 blob 的容器。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

容器名称必须以字母或数字开头，并且只能包含字母、数字和连字符 (-)。 有关命名 Blob 和容器的更多规则，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

## <a name="enable-public-read-access-for-a-container"></a>启用容器的公共读取权限

默认情况下，新创建的容器是专用容器。 也就是说，如果没有[共享访问签名](#create-a-shared-access-signature-sas)或存储帐户的访问密钥，就无法访问此容器。 使用 Azure CLI，可以通过将容器权限设置为以下三个级别之一来修改此行为：

| | |
|---|---|
| `--public-access off` | （默认值）无公共读取访问权限 |
| `--public-access blob` | 只有 Blob 的公共读取访问权限 |
| `--public-access container` | Blob 的公共读取访问权限，可以列出容器中的 Blob |

设置 `blob` 或 `container` 的公共访问权限后，就为 Internet 上的所有人启用了只读访问权限。 例如，如果想要在网站上显示已存储为 blob 的图像，则需要启用公共读取访问权限。 如果想要启用读取/写入访问权限，必须改为使用[共享访问签名 (SAS)](#create-a-shared-access-signature-sas)。

可使用 [az storage container set-permission](/cli/azure/storage/container#create) 命令启用容器的公共读取访问权限。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>将 Blob 上传到容器中

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 是 Azure 存储中存储的最常见 blob 类型。 在必须将数据添加到现有的 blob 中且不能修改该 blob 的现有内容时（例如进行日志记录时），使用追加 blob。 页 blob 支持 IaaS 虚拟机的 VHD 文件。

本示例使用 [az storage blob upload](/cli/azure/storage/blob#upload) 命令将 blob 上传到在上一个步骤中创建的容器中。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

此操作将创建 Blob（如果该 Blob 尚不存在），或者覆盖 Blob（如果该 Blob 已存在）。 上传几个文件，以便在后续步骤中列出 Blob 时可以看到多个条目。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob#list) 命令列出容器中的 blob。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

示例输出：

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>下载 Blob

使用 [az storage blob download](/cli/azure/storage/blob#download) 命令下载在上一步中上传的 blob。

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>在存储帐户之间复制 blob

可以在存储帐户和区域内或跨存储帐户和区域异步复制 Blob。

以下示例演示如何将一个存储帐户中的 Blob 复制到另一个存储帐户。 我们首先在源存储帐户中创建一个容器，指定对其 blob 的公共读取访问权限。 接下来，我们将文件上传到该容器，最后，将 blob 从该容器复制到目标存储帐户中的一个容器。

在本示例中，目标容器必须已存在于目标存储帐户中，复制操作才会成功。 另外，在 `--source-uri` 参数中指定的源 blob 必须包括一个共享的访问签名 (SAS) 令牌，或者可供公开访问，如此示例中所示。

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>删除 Blob

可使用 [az storage blob delete](/cli/azure/storage/blob#delete) 命令从容器中删除 blob。

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>显示和修改 blob 属性和元数据

每个 blob 都有多个由服务定义的属性，可以使用 [az storage blob show](/cli/azure/storage/blob#show) 命令显示这些属性，其中包括名称、类型、长度等。 还可以使用 [az storage blob metadata update](/cli/azure/storage/blob/metadata#update) 命令为 blob 配置自己的属性及属性值。

本示例首先显示 blob 的由服务定义的属性，然后会使用两个属于我们自己的元数据属性更新该 blob。 最后使用 [az storage blob metadata show](/cli/azure/storage/blob/metadata#show) 命令显示 blob 的元数据属性及其值。

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>创建共享访问签名 (SAS)

共享访问签名 (SAS) 提供一种授予对存储帐户中对象的受限访问权限而不必公开你的存储帐户访问密钥的方法。 若要生成可实现访问专用资源的 URI，需要创建具有所需的权限及有效时间范围（有效期）的 SAS 令牌，然后将其作为查询字符串追加到资源的 URL，以此方式生成它的完整 SAS URI。 然后，拥有此 SAS URI（资源的 URL 与 SAS 令牌的组合）的所有人均可在该 SAS 令牌的有效期内访问它。 例如，你可能希望提供专用 Blob 2 分钟的读取权限，以便用户可以查看。

在后续步骤中，将禁用容器的公共访问权限、测试仅专用访问权限并生成和测试 SAS URI。

### <a name="disable-container-public-access"></a>禁用容器公共访问权限

首先，将容器的访问级别设置为 `off`。 此设置将指定没有共享访问签名或存储帐户访问密钥就不能访问容器或其 blob。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>验证专用访问

若要验证是否没有对该容器中的 blob 的公共读取访问权限，请使用 [az storage blob url](/cli/azure/storage/blob#url) 命令获取其中一个 blob 的 URL。

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

在私密浏览器窗口中导航到该 blob 的 URL。 你将看到 `ResourceNotFound` 错误，因为该 blob 是专用的，而你尚未加入共享访问签名。

### <a name="create-a-sas-uri"></a>创建 SAS URI

现在我们创建允许访问该 blob 的 SAS URI。 在接下来的示例中，首先使用通过 [az storage blob url](/cli/azure/storage/blob#url) 获取的 blob URL 填充一个变量，然后使用通过 [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas) 命令生成的 SAS 令牌填充另一个变量。 最后，通过串联这两个变量（由 `?` 查询字符串分隔符 分隔）输出该 blob 的完整 SAS URI。

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>测试 SAS URI

在私密浏览器窗口中，导航到上一个代码段中使用 `echo` 命令显示的完整 SAS URI。 这次，你不会看到错误，而是可以查看或下载该 blob。

等待足够长的时间让 URL 过期（在本例中为 2 分钟），然后在另一个私密浏览器窗口中导航到该 URI。 现在你会看到 `AuthenticationFailed` 错误，因为 SAS 令牌已过期。

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括使用本教程创建的存储帐户及上传的任何一个 blob），可使用 [az group delete](/cli/azure/group#delete) 命令删除该资源组。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

此教程介绍了有关使用 Azure 存储中的 blob 的基础知识：

> [!div class="checklist"]
> * 创建容器
> * 将文件 (blob) 上传到容器中
> * 列出容器中的 Blob
> * 从容器下载 Blob
> * 在存储帐户之间复制 blob
> * 删除 Blob
> * 显示和修改 blob 属性和元数据
> * 使用共享访问签名 (SAS) 管理安全性

以下资源介绍了有关使用 Azure CLI 及使用存储帐户中的资源的其他信息。

* Azure CLI
  * [使用 Azure CLI 2.0 进行登录](/cli/azure/authenticate-azure-cli) - 了解使用 CLI 进行身份验证的不同方法，其中包括通过用于运行无人参与的 Azure CLI 脚本的[服务主体](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal)进行的非交互式登录。
  * [Azure CLI 2.0 命令参考](/cli/azure/)
* Microsoft Azure 存储资源管理器
  * [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
