---
title: 使用 AzCopy v10 将数据传输到 Azure Blob 存储或从 Azure Blob 存储传输数据 |Microsoft Docs
description: 本文包含可帮助您在本地文件系统和容器之间创建容器、复制文件和同步目录的 AzCopy 示例命令的集合。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: eead4436a7135f6f74528cde52883ea247360fc6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648778"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>用 AzCopy 和 Blob 存储传输数据

AzCopy 是一个命令行实用工具, 可用于在存储帐户之间复制数据。 本文包含适用于 Blob 存储的示例命令。

## <a name="get-started"></a>开始使用

请参阅[AzCopy 文章入门](storage-use-azcopy-v10.md), 下载 AzCopy 并了解你可以向存储服务提供授权凭据的方式。

> [!NOTE]
> 本文中的示例假定你已使用`AzCopy login`命令对标识进行身份验证。 然后, AzCopy 使用 Azure AD 帐户来授权访问 Blob 存储中的数据。
>
> 如果你希望使用 SAS 令牌来授予对 blob 数据的访问权限, 则可以将该令牌附加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`。

## <a name="create-a-container"></a>创建容器

可以使用 AzCopy `make`命令创建容器。 本节中的示例将创建一个名为`mycontainer`的容器。

|    |     |
|--------|-----------|
| **语法** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **示例** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **示例**(分层命名空间) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>上载文件

可以使用 AzCopy `copy`命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 使用通配符上载文件

> [!NOTE]
> AzCopy 不会自动计算和存储文件的 md5 哈希代码。 如果希望 AzCopy 执行此操作, 请将`--put-md5`标志追加到每个复制命令。 这样一来, 在下载 blob 时, AzCopy 会计算已下载数据的 md5 哈希, 并验证存储在 blob 的`Content-md5`属性中的 md5 哈希是否与计算所得的哈希匹配。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **示例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **示例**(分层命名空间) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 默认情况下，AzCopy 将数据上传到块 Blob 中。 若要将文件作为追加 Blob 或页 Blob 上传，请使用 `--blob-type=[BlockBlob|PageBlob|AppendBlob]` 标志。

### <a name="upload-a-directory"></a>上传目录

此示例将目录 (以及该目录中的所有文件) 复制到 blob 容器。 结果是容器中具有相同名称的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **示例**(分层命名空间) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

若要复制到容器中的目录, 只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **示例**(分层命名空间) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

如果指定容器中不存在的目录的名称, 则 AzCopy 将按该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容, 而无需使用通配符 (*) 复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **示例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **示例**(分层命名空间) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> `--recursive`追加标志以便上传所有子目录中的文件。

## <a name="download-files"></a>下载文件

你可以使用 AzCopy `copy`命令将 blob、目录和容器下载到你的本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 使用通配符下载文件

> [!NOTE]
> 如果 blob `Content-md5`的属性值包含哈希, 则 AzCopy 将计算已下载数据的 md5 哈希值, 并验证存储在 blob 的`Content-md5`属性中的 md5 哈希是否与计算所得的哈希匹配。 如果这些值不匹配, 则下载将失败, 除非通过追加`--check-md5=NoCheck`或`--check-md5=LogOnly`复制命令来重写此行为。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **示例**(分层命名空间) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **示例**(分层命名空间) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

此示例将生成一个名为`C:\myDirectory\myBlobDirectory`的目录, 其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

你可以通过使用通配符 (*) 来下载目录的内容, 而无需复制包含目录本身。

> [!NOTE]
> 目前, 只有没有分层命名空间的帐户才支持此方案。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **示例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> `--recursive`追加标志以下载所有子目录中的文件。

## <a name="copy-blobs-between-storage-accounts"></a>在存储帐户之间复制 blob

可以使用 AzCopy 将 blob 复制到其他存储帐户。 复制操作是同步的, 因此在命令返回时, 这表示已复制所有文件。

AzCopy 使用[服务器到服务器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), 因此直接在存储服务器之间复制数据。 这些复制操作不会使用计算机的网络带宽。 通过设置`AZCOPY_CONCURRENCY_VALUE`环境变量的值, 可以增加这些操作的吞吐量。 若要了解详细信息, 请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此方案在当前版本中具有以下限制。
>
> - 仅支持没有分层命名空间的帐户。
> - 必须向每个源 URL 追加一个 SAS 令牌。 如果使用 Azure Active Directory (AD) 提供授权凭据, 则只能从目标 URL 中省略 SAS 令牌。
>-  高级块 blob 存储帐户不支持访问层。 通过将设置`s2s-preserve-access-tier`为`false` (例如: `--s2s-preserve-access-tier=false`), 从复制操作中省略 blob 的访问层。

本部分包含以下示例：

> [!div class="checklist"]
> * 将 blob 复制到其他存储帐户
> * 将目录复制到其他存储帐户
> * 将容器复制到其他存储帐户
> * 将所有容器、目录和文件复制到其他存储帐户

### <a name="copy-a-blob-to-another-storage-account"></a>将 blob 复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **示例** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>将容器复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>将所有容器、目录和 blob 复制到另一个存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **示例** | `azcopy copy "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>同步文件

你可以使用 blob 容器同步本地文件系统的内容。 同步是单向的。 换句话说, 您可以选择这两个终结点中的哪一个是源, 哪一个是目标。

> [!NOTE]
> 目前, 只有没有分层命名空间的帐户才支持此方案。 当前版本的 AzCopy 不在其他源和目标之间同步 (例如:文件存储或 Amazon Web Services (AWS) S3 存储桶)。

此`sync`命令比较文件名和上次修改时间戳。 将可选标志设置为的`true`值`prompt` , 如果这些文件不再存在于源目录中, 则将其删除。 `--delete-destination`

如果将`--delete-destination`标志设置为`true` AzCopy, 则会删除文件而不提供提示。 如果希望在 AzCopy 删除文件之前出现提示, 请将`--delete-destination`标志设置为。 `prompt`

> [!NOTE]
> 若要防止意外删除, 请在使用`--delete-destination=prompt|true`标志之前确保启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用对本地文件系统的更改更新容器

在这种情况下, 容器为目标, 本地文件系统是源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **示例** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用对容器所做的更改更新本地文件系统

在这种情况下, 本地文件系统为目标, 容器为源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **示例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例:

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)
