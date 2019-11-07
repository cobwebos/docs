---
title: 使用 AzCopy v10 将数据传入或传出 Azure 文件 |Microsoft Docs
description: 传输数据与 AzCopy 和文件存储。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 225fcd475d488cedb8bd210fe2fa9371849314ac
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615517"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>用 AzCopy 和文件存储传输数据 

AzCopy 是一个命令行实用工具，可用于在存储帐户中复制 blob 或文件。 本文包含适用于 Azure 文件的示例命令。

在开始之前，请参阅[AzCopy 文章入门](storage-use-azcopy-v10.md)，下载 AzCopy 并熟悉该工具。

## <a name="create-file-shares"></a>创建文件共享

可以使用[azcopy make](storage-ref-azcopy-make.md)命令创建文件共享。 本部分中的示例将创建一个名为 `myfileshare` 的文件共享。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

|    |     |
|--------|-----------|
| **语法** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **示例** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

有关详细的参考文档，请参阅[azcopy](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上传文件

可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 上传目录的内容
> * 上传特定文件

> [!NOTE]
> AzCopy 不会自动计算和存储文件的 md5 哈希代码。 如果希望 AzCopy 执行此操作，请将 `--put-md5` 标志追加到每个复制命令。 这样，在下载文件时，AzCopy 将计算已下载数据的 MD5 哈希，并验证存储在文件 `Content-md5` 属性中的 MD5 哈希是否与计算所得的哈希匹配。

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **示例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

还可以通过在文件路径或文件名中的任意位置使用通配符（*）来上传文件。 例如： `'C:\myDirectory\*.txt'` 或 `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>上传目录

此示例将目录（以及该目录中的所有文件）复制到文件共享。 结果是文件共享中名称相同的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

若要复制到文件共享中的目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

如果指定文件共享中不存在的目录的名称，则 AzCopy 将按该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容，而无需使用通配符（*）复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 追加 `--recursive` 标志以上载所有子目录中的文件。

### <a name="upload-specific-files"></a>上传特定文件

您可以指定完整的文件名，或使用带有通配符（*）的部分名称。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整的文件名

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-path` 选项一起使用。 使用分号（`;`）分隔单个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

在此示例中，AzCopy 传输 `C:\myDirectory\photos` 目录和 `C:\myDirectory\documents\myFile.txt` 文件。 需要包含 `--recursive` 选项以传输 `C:\myDirectory\photos` 目录中的所有文件。

还可以通过使用 `--exclude-path` 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-pattern` 选项一起使用。 指定包含通配符的部分名称。 使用 semicolin （`;`）分隔名称。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

还可以通过使用 `--exclude-pattern` 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  如果要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 以获取所有文本文件。

## <a name="download-files"></a>下载文件

你可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令将文件、目录和文件共享下载到你的本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 下载目录的内容
> * 下载特定文件

> [!NOTE]
> 如果文件的 `Content-md5` 属性值包含哈希，则 AzCopy 将计算已下载数据的 MD5 哈希，并验证存储在文件的 `Content-md5` 属性中的 MD5 哈希是否与计算的哈希匹配。 如果这些值不匹配，则下载将失败，除非通过将 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 追加到复制命令来重写此行为。

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>?<SAS-token>' '<local-file-path>'` |
| **示例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | "azcopy copy" https：//< storage-name > < 文件共享-名称 >/< 目录路径 >？ < SAS 令牌 > " 
"< 本地目录路径 >"--recursive " |
| **示例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

此示例将生成一个名为 `C:\myDirectory\myFileShareDirectory` 的目录，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

你可以通过使用通配符（*）来下载目录的内容，而无需复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **示例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> 追加 `--recursive` 标志以下载所有子目录中的文件。

### <a name="download-specific-files"></a>下载特定文件

您可以指定完整的文件名，或使用带有通配符（*）的部分名称。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整的文件名

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-path` 选项一起使用。 使用 semicolin （`;`）分隔单个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 传输 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目录和 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 文件。 需要包含 `--recursive` 选项以传输 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目录中的所有文件。

还可以通过使用 `--exclude-path` 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

将[azcopy copy](storage-ref-azcopy-copy.md)命令与 `--include-pattern` 选项一起使用。 指定包含通配符的部分名称。 使用 semicolin （`;`）分隔名称。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

还可以通过使用 `--exclude-pattern` 选项排除文件。 若要了解详细信息，请参阅[azcopy 复制](storage-ref-azcopy-copy.md)参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  如果要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 以获取所有文本文件。

## <a name="copy-files-between-storage-accounts"></a>在存储帐户之间复制文件

可以使用 AzCopy 将文件复制到其他存储帐户。 复制操作是同步的，因此在命令返回时，这表示已复制所有文件。

AzCopy 使用[服务器到服务器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此直接在存储服务器之间复制数据。 这些复制操作不会使用计算机的网络带宽。 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值来增加这些操作的吞吐量。 若要了解详细信息，请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。

本部分包含以下示例：

> [!div class="checklist"]
> * 将文件复制到其他存储帐户
> * 将目录复制到其他存储帐户
> * 将文件共享复制到其他存储帐户
> * 将所有文件共享、目录和文件复制到其他存储帐户

有关详细的参考文档，请参阅[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="copy-a-file-to-another-storage-account"></a>将文件复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **示例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>将文件共享复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>将所有文件共享、目录和文件复制到其他存储帐户

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **示例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>同步文件

你可以将文件共享的内容与另一个文件共享进行同步。 你还可以将文件共享中的目录内容与位于另一个文件共享中的目录的内容进行同步。 同步是单向的。 换句话说，您可以选择这两个终结点中的哪一个是源，哪一个是目标。 同步还使用服务器到服务器 Api。

> [!NOTE]
> 目前，只有没有分层命名空间的帐户才支持此方案。 当前版本的 AzCopy 不在 Azure 文件和 Blob 存储之间同步。

[Sync](storage-ref-azcopy-sync.md)命令比较文件名和上次修改时间戳。 将 `--delete-destination` 可选标志设置为值为 `true` 或 `prompt`，以便在目标目录中删除文件时删除目标目录中的文件。

如果将 `--delete-destination` 标志设置为 `true` AzCopy 将删除文件而不提供提示。 如果希望在 AzCopy 删除文件之前出现提示，请将 `--delete-destination` 标志设置为 `prompt`。

有关详细的参考文档，请参阅[azcopy 同步](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>使用对其他文件共享所做的更改更新文件共享

此命令中显示的第一个文件共享是源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用对其他文件共享中的目录的更改更新目录

此命令中显示的第一个目录是源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)
