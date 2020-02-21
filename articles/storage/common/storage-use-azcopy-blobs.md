---
title: 使用 AzCopy v10 将数据传输到 Azure Blob 存储或从 Azure Blob 存储传输数据 |Microsoft Docs
description: 本文包含可帮助您在本地文件系统和容器之间创建容器、复制文件和同步目录的 AzCopy 示例命令的集合。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f218c64d3ffe4955877516551a29376372144598
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526716"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>用 AzCopy 和 Blob 存储传输数据

AzCopy 是一个命令行实用工具，可用于在存储帐户之间复制数据。 本文包含适用于 Blob 存储的示例命令。

## <a name="get-started"></a>入门

请参阅[AzCopy 文章入门](storage-use-azcopy-v10.md)，下载 AzCopy 并了解你可以向存储服务提供授权凭据的方式。

> [!NOTE]
> 本文中的示例假设已使用 `AzCopy login` 命令对标识进行身份验证。 然后，AzCopy 使用 Azure AD 帐户来授权访问 Blob 存储中的数据。
>
> 如果你希望使用 SAS 令牌来授予对 blob 数据的访问权限，则可以将该令牌附加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="create-a-container"></a>创建容器

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

可以使用[azcopy make](storage-ref-azcopy-make.md)命令创建容器。 本节中的示例将创建一个名为 `mycontainer`的容器。

|    |     |
|--------|-----------|
| **语法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **示例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **示例**（分层命名空间） | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

有关详细的参考文档，请参阅[azcopy](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上传文件

可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 上传目录的内容 
> * 上传特定文件

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **示例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

还可以通过在文件路径或文件名中的任意位置使用通配符（*）来上传文件。 例如： `'C:\myDirectory\*.txt'`或 `C:\my*\*.txt`。

> [!NOTE]
> 默认情况下，AzCopy 将数据上传到块 blob。 若要将文件上传为追加 Blob 或页 Blob，请使用标志 `--blob-type=[BlockBlob|PageBlob|AppendBlob]`。

### <a name="upload-a-directory"></a>上传目录

此示例将目录（以及该目录中的所有文件）复制到 blob 容器。 结果是容器中具有相同名称的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

若要复制到容器中的目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果指定容器中不存在的目录的名称，则 AzCopy 将按该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容，而无需使用通配符（*）复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 追加 `--recursive` 标志以便上传所有子目录中的文件。

### <a name="upload-specific-files"></a>上传特定文件

您可以指定完整的文件名，或使用带有通配符（*）的部分名称。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整的文件名

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-path` 选项一起使用。 使用分号（`;`）分隔单个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 传输 `C:\myDirectory\photos` 目录和 `C:\myDirectory\documents\myFile.txt` 文件。 需要包含 `--recursive` 选项以传输 `C:\myDirectory\photos` 目录中的所有文件。

你还可以使用 "`--exclude-path`" 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-pattern` 选项一起使用。 指定包含通配符的部分名称。 使用 semicolin （`;`）分隔名称。 

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

你还可以使用 "`--exclude-pattern`" 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  如果要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 以获取所有文本文件。

## <a name="download-files"></a>下载文件

你可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令将 blob、目录和容器下载到你的本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 下载目录的内容
> * 下载特定文件

> [!NOTE]
> 如果 blob 的 `Content-md5` 属性值包含哈希，则 AzCopy 将计算已下载数据的 MD5 哈希，并验证存储在 blob 的 `Content-md5` 属性中的 MD5 哈希是否与计算的哈希匹配。 如果这些值不匹配，则下载将失败，除非通过将 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 追加到复制命令来重写此行为。

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此示例将生成一个名为 `C:\myDirectory\myBlobDirectory` 的目录，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

你可以通过使用通配符（*）来下载目录的内容，而无需复制包含目录本身。

> [!NOTE]
> 目前，只有没有分层命名空间的帐户才支持此方案。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 追加 `--recursive` 标志以下载所有子目录中的文件。

### <a name="download-specific-files"></a>下载特定文件

您可以指定完整的文件名，或使用带有通配符（*）的部分名称。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整的文件名

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-path` 选项一起使用。 使用 semicolin （`;`）分隔单个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此示例中，AzCopy 传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录和 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 文件。 需要包含 `--recursive` 选项以传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录中的所有文件。

你还可以使用 "`--exclude-path`" 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-pattern` 选项一起使用。 指定包含通配符的部分名称。 使用 semicolin （`;`）分隔名称。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

你还可以使用 "`--exclude-pattern`" 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  如果要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 以获取所有文本文件。

## <a name="copy-blobs-between-storage-accounts"></a>在存储帐户之间复制 blob

可以使用 AzCopy 将 blob 复制到其他存储帐户。 复制操作是同步的，因此在命令返回时，这表示已复制所有文件。 

AzCopy 使用[服务器到服务器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此直接在存储服务器之间复制数据。 这些复制操作不会使用计算机的网络带宽。 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值来增加这些操作的吞吐量。 若要了解详细信息，请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此方案在当前版本中具有以下限制。
>
> - 必须向每个源 URL 追加一个 SAS 令牌。 如果使用 Azure Active Directory （AD）提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。
>-  高级块 blob 存储帐户不支持访问层。 通过将 `s2s-preserve-access-tier` 设置为 `false` （例如： `--s2s-preserve-access-tier=false`），从复制操作中省略 blob 的访问层。

本部分包含以下示例：

> [!div class="checklist"]
> * 将 blob 复制到其他存储帐户
> * 将目录复制到其他存储帐户
> * 将容器复制到其他存储帐户
> * 将所有容器、目录和文件复制到其他存储帐户

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

 这些示例还适用于具有分层命名空间的帐户。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md)使你可以在这些帐户上使用相同的 URL 语法（`blob.core.windows.net`）。 

### <a name="copy-a-blob-to-another-storage-account"></a>将 blob 复制到其他存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到其他存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>将容器复制到其他存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>将所有容器、目录和 blob 复制到另一个存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>同步文件

你可以使用 blob 容器同步本地文件系统的内容。 你还可以将容器和虚拟目录彼此同步。 同步是单向的。 换句话说，您可以选择这两个终结点中的哪一个是源，哪一个是目标。 同步还使用服务器到服务器 Api。 本节中介绍的示例也适用于具有分层命名空间的帐户。 

> [!NOTE]
> 当前版本的 AzCopy 不在其他源和目标之间同步（例如：文件存储或 Amazon Web Services （AWS） S3 存储桶）。

[Sync](storage-ref-azcopy-sync.md)命令比较文件名和上次修改时间戳。 将 `--delete-destination` 可选标志设置为 `true` 或 `prompt` 的值，以便在目标目录中删除文件时删除这些文件。

如果将 `--delete-destination` 标志设置为 `true` AzCopy 将删除文件而不提供提示。 如果希望在 AzCopy 删除文件之前出现提示，请将 `--delete-destination` 标志设置为 `prompt`。

> [!NOTE]
> 若要防止意外删除，请在使用 `--delete-destination=prompt|true` 标志之前确保启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能。

有关详细的参考文档，请参阅[azcopy 同步](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用对本地文件系统的更改更新容器

在这种情况下，容器为目标，本地文件系统是源。 

|    |     |
|--------|-----------|
| **语法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用对容器所做的更改更新本地文件系统

在这种情况下，本地文件系统为目标，容器为源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **示例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>使用其他容器中的更改更新容器

此命令中显示的第一个容器为源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用对其他文件共享中的目录的更改更新目录

此命令中显示的第一个目录是源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)
