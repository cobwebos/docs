---
title: 使用 AzCopy v10 将向 / 从 Azure Blob 存储的数据传输 |Microsoft Docs
description: 本文包含一系列 AzCopy 命令，可以帮助您创建容器，复制文件，并同步本地文件系统和容器之间的文件夹的示例。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299405"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 存储传输数据

AzCopy 是一个命令行实用工具，可用于将数据复制到，或存储帐户之间。 本文包含使用 Blob 存储的示例命令。

## <a name="get-started"></a>开始使用

请参阅[开始使用 AzCopy](storage-use-azcopy-v10.md)文章以下载 AzCopy 并了解您可以向存储服务的授权凭据的方法。

> [!NOTE]
> 这篇文章中的示例假定你已通过你的身份使用身份验证`AzCopy login`命令。 AzCopy 然后使用你的 Azure AD 帐户授予对 Blob 存储中的数据访问权限。
>
> 如果你将使用 SAS 令牌来授予对 blob 数据的访问权限，可以将该令牌附加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`。

## <a name="create-a-container"></a>创建容器

可以使用 AzCopy`make`命令以创建容器。 本部分中的示例创建名为的容器`mycontainer`。

|    |     |
|--------|-----------|
| **语法** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **示例** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **示例**（层次结构命名空间） | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>上传文件

可以使用 AzCopy`copy`命令将上传文件和文件夹从本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传文件夹
> * 将文件上传通过使用通配符

> [!NOTE]
> AzCopy 不会自动计算并存储该文件的 md5 哈希代码。 如果你想要执行此操作的 AzCopy，然后追加`--put-md5`每个复制操作命令的标志。 这样一来，下载 blob 后，AzCopy 计算的 MD5 哈希下载数据，并验证 MD5 哈希值存储在 blob 的`Content-md5`属性与计算的哈希匹配。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **示例** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 默认情况下，AzCopy 将数据上传到块 Blob 中。 若要将文件作为追加 Blob 或页 Blob 上传，请使用 `--blob-type=[BlockBlob|PageBlob|AppendBlob]` 标志。

### <a name="upload-a-folder"></a>上传文件夹

此示例将一个文件夹 （和所有在该文件夹中的文件） 复制到 blob 容器。 结果是同名的容器中的文件夹。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

若要复制到容器中的文件夹，只需在命令字符串中指定该文件夹的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

如果指定的容器中不存在的文件夹名称时，AzCopy 将创建该名称的新文件夹。

### <a name="upload-the-contents-of-a-folder"></a>上传文件夹的内容

你可以上载文件夹的内容，而不使用通配符 （*） 复制包含文件夹本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **示例** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> 追加`--recursive`标志，用于上传所有子文件夹中的文件。

## <a name="download-files"></a>下载文件

可以使用 AzCopy`copy`命令，下载到本地计算机的 blob、 文件夹和容器。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载文件夹
> * 使用通配符字符下载文件

> [!NOTE]
> 如果`Content-md5`blob 的属性值包含哈希值，AzCopy 计算下载的数据的 MD5 哈希，并验证 MD5 哈希值存储在 blob 的`Content-md5`属性与计算的哈希匹配。 如果这些值不匹配，除非通过追加重写此行为，则下载会失败`--check-md5=NoCheck`或`--check-md5=LogOnly`复制操作命令。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **示例**（层次结构命名空间） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>下载文件夹

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

此示例将返回名为的文件夹`C:\myFolder\myBlobFolder`，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-folder"></a>下载文件夹的内容

您可以下载文件夹的内容而不使用通配符 （*） 复制包含文件夹本身。

> [!NOTE]
> 目前，只能用于帐户没有分层命名空间支持此方案。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> 追加`--recursive`标志，用于下载所有子文件夹中的文件。

## <a name="copy-blobs-between-storage-accounts"></a>存储帐户之间复制 blob

可以使用 AzCopy 将 blob 复制到其他存储帐户。 复制操作是同步的因此时该命令将返回，则表示已复制的所有文件。

> [!NOTE]
> 目前，只能用于帐户没有分层命名空间支持此方案。

AzCopy 将使用[放置块从 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，因此直接存储服务器之间复制数据。 这些复制操作不使用您的计算机的网络带宽。

本部分包含以下示例：

> [!div class="checklist"]
> * 将 blob 复制到另一个存储帐户
> * 将文件夹复制到另一个存储帐户
> * 将容器复制到另一个存储帐户
> * 将所有容器、 文件夹和文件都复制到另一个存储帐户

### <a name="copy-a-blob-to-another-storage-account"></a>将 blob 复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>将文件夹复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>将容器复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>将所有容器、 文件夹和文件都复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>同步的文件

你可以同步到 blob 容器的本地文件系统的内容。 你还可以在计算机上同步到本地文件系统的 blob 容器。 同步是单向的。 换而言之，你选择哪个这些两个终结点是源，哪个订阅是目标。

> [!NOTE]
> 当前版本的 AzCopy 不会同步其他源和目标之间 (例如：文件存储或 Amazon Web Services (AWS) S3 存储桶）。

`sync`命令比较文件的名称和上次修改时间戳。 设置`--delete-destination`的值的可选标志`true`或`prompt`如果这些文件不再存在的源文件夹中删除目标文件夹中的文件。

如果您设置`--delete-destination`标记，用于`true`AzCopy 删除文件，而无需提供一个提示。 如果你希望文件出现在 AzCopy 删除之前的提示，设置`--delete-destination`标记，用于`prompt`。

> [!NOTE]
> 若要防止意外删除，请确保启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能在使用之前`--delete-destination=prompt|true`标志。

### <a name="synchronize-a-container-to-a-local-file-system"></a>同步到本地文件系统容器

在这种情况下，本地文件系统将成为源，并且该容器是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **示例**（层次结构命名空间） | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>同步到容器的本地文件系统

在这种情况下，容器将成为源和本地文件系统是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **示例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **示例**（层次结构命名空间） | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>后续步骤

在以下这些文章中找到更多示例：

- [开始使用 AzCopy](storage-use-azcopy-v10.md)

- [教程：迁移的本地数据发送到云存储使用 AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 存储桶传输数据](storage-use-azcopy-s3.md)

- [配置、 优化和故障排除 AzCopy](storage-use-azcopy-configure.md)