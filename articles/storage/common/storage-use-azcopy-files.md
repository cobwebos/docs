---
title: 使用 AzCopy v10 将数据传入或传出 Azure 文件 |Microsoft Docs
description: 传输数据与 AzCopy 和文件存储。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648757"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>用 AzCopy 和文件存储传输数据 

AzCopy 是一个命令行实用工具, 可用于在存储帐户中复制 blob 或文件。 本文包含适用于 Azure 文件的示例命令。

在开始之前, 请参阅[AzCopy 文章入门](storage-use-azcopy-v10.md), 下载 AzCopy 并熟悉该工具。

## <a name="create-file-shares"></a>创建文件共享

可以使用 AzCopy `make`命令创建文件共享。 本部分中的示例将创建一个名为`myfileshare`的文件共享。

|    |     |
|--------|-----------|
| **语法** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **示例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>上载文件

可以使用 AzCopy `copy`命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 使用通配符上载文件

> [!NOTE]
> AzCopy 不会自动计算和存储文件的 md5 哈希代码。 如果希望 AzCopy 执行此操作, 请将`--put-md5`标志追加到每个复制命令。 这样一来, 文件下载时, AzCopy 会计算已下载数据的 md5 哈希, 并验证存储在文件的`Content-md5`属性中的 md5 哈希是否与计算所得的哈希匹配。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **示例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>上传目录

此示例将目录 (以及该目录中的所有文件) 复制到文件共享。 结果是文件共享中名称相同的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

若要复制到文件共享中的目录, 只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

如果指定文件共享中不存在的目录的名称, 则 AzCopy 将按该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容, 而无需使用通配符 (*) 复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **示例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> `--recursive`追加标志以便上传所有子目录中的文件。

## <a name="download-files"></a>下载文件

你可以使用 AzCopy `copy`命令将文件、目录和文件共享下载到你的本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 使用通配符下载文件

> [!NOTE]
> 如果文件`Content-md5`的属性值包含哈希, 则 AzCopy 将计算已下载数据的 md5 哈希值, 并验证存储在文件的`Content-md5`属性中的 md5 哈希是否与计算所得的哈希匹配。 如果这些值不匹配, 则下载将失败, 除非通过追加`--check-md5=NoCheck`或`--check-md5=LogOnly`复制命令来重写此行为。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **示例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **示例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

此示例将生成一个名为`C:\myDirectory\myFileShareDirectory`的目录, 其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

你可以通过使用通配符 (*) 来下载目录的内容, 而无需复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **示例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> `--recursive`追加标志以下载所有子目录中的文件。

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例:

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)