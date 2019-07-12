---
title: 使用 AzCopy v10 将向 / 从 Azure Blob 存储的数据传输 |Microsoft Docs
description: 本文包含一系列 AzCopy 命令，可以帮助您创建容器，复制文件，并同步本地文件系统和容器之间的目录的示例。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 83e32a1e8f77604330a9f3aba0e011a0a0851e2f
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625608"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 存储传输数据

AzCopy 是一个命令行实用工具，可用于将数据复制到，或存储帐户之间。 本文包含使用 Blob 存储的示例命令。

## <a name="get-started"></a>入门

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

可以使用 AzCopy`copy`命令将上传文件和目录从本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 将文件上传通过使用通配符

> [!NOTE]
> AzCopy 不会自动计算并存储该文件的 md5 哈希代码。 如果你想要执行此操作的 AzCopy，然后追加`--put-md5`每个复制操作命令的标志。 这样一来，下载 blob 后，AzCopy 计算的 MD5 哈希下载数据，并验证 MD5 哈希值存储在 blob 的`Content-md5`属性与计算的哈希匹配。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **示例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 默认情况下，AzCopy 将数据上传到块 Blob 中。 若要将文件作为追加 Blob 或页 Blob 上传，请使用 `--blob-type=[BlockBlob|PageBlob|AppendBlob]` 标志。

### <a name="upload-a-directory"></a>上传目录

此示例将一个目录 （及其所有该目录中的文件） 复制到 blob 容器。 结果是同名的容器中的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

若要复制到容器中的目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

如果指定的容器中不存在的目录名称，AzCopy 将创建该名称的新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容，而不使用通配符 （*） 复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **示例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **示例**（层次结构命名空间） | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> 追加`--recursive`标志，用于上传所有子目录中的文件。

## <a name="download-files"></a>下载文件

可以使用 AzCopy`copy`命令，下载到本地计算机的 blob、 目录和容器。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 使用通配符字符下载文件

> [!NOTE]
> 如果`Content-md5`blob 的属性值包含哈希值，AzCopy 计算下载的数据的 MD5 哈希，并验证 MD5 哈希值存储在 blob 的`Content-md5`属性与计算的哈希匹配。 如果这些值不匹配，除非通过追加重写此行为，则下载会失败`--check-md5=NoCheck`或`--check-md5=LogOnly`复制操作命令。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **示例**（层次结构命名空间） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **示例**（层次结构命名空间） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

此示例生成一个名为目录`C:\myDirectory\myBlobDirectory`，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

您可以下载目录的内容，而不使用通配符 （*） 复制包含目录本身。

> [!NOTE]
> 目前，只能用于帐户没有分层命名空间支持此方案。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> 追加`--recursive`标志，用于下载所有子目录中的文件。

## <a name="copy-blobs-between-storage-accounts"></a>存储帐户之间复制 blob

可以使用 AzCopy 将 blob 复制到其他存储帐户。 复制操作是同步的因此时该命令将返回，则表示已复制的所有文件。

> [!NOTE]
> 目前，只能用于帐户没有分层命名空间支持此方案。 

AzCopy 将使用[放置块从 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，因此直接存储服务器之间复制数据。 这些复制操作不使用您的计算机的网络带宽。

本部分包含以下示例：

> [!div class="checklist"]
> * 将 blob 复制到另一个存储帐户
> * 将目录复制到另一个存储帐户
> * 将容器复制到另一个存储帐户
> * 将所有容器、 目录和文件都复制到另一个存储帐户

> [!NOTE]
> 在当前版本中，您需要将 SAS 令牌附加到每个源 URL。 如果使用 Azure Active Directory (AD) 提供授权凭据，则可以省略只能从目标 URL 的 SAS 令牌。 

### <a name="copy-a-blob-to-another-storage-account"></a>将 blob 复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>将容器复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>将所有容器、 目录和文件都复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **示例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>同步的文件

可以通过 blob 容器中同步本地文件系统的内容。 同步是单向的。 换而言之，你选择哪个这些两个终结点是源，哪个订阅是目标。

> [!NOTE]
> 目前，只能用于帐户没有分层命名空间支持此方案。 当前版本的 AzCopy 不会同步其他源和目标之间 (例如：文件存储或 Amazon Web Services (AWS) S3 存储桶）。

`sync`命令比较文件的名称和上次修改时间戳。 设置`--delete-destination`的值的可选标志`true`或`prompt`若要删除的目标目录中的文件，如果这些文件不存在于源目录。

如果您设置`--delete-destination`标记，用于`true`AzCopy 删除文件，而无需提供一个提示。 如果你希望文件出现在 AzCopy 删除之前的提示，设置`--delete-destination`标记，用于`prompt`。

> [!NOTE]
> 若要防止意外删除，请确保启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能在使用之前`--delete-destination=prompt|true`标志。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>更新具有对本地文件系统的更改的容器

在这种情况下，容器是目标，并且本地文件系统是源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>对容器进行的更改更新本地文件系统

在这种情况下，本地文件系统是目标，而容器是源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **示例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>后续步骤

在以下这些文章中找到更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [教程：迁移的本地数据发送到云存储使用 AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)
