---
title: 可以使用 AzCopy v10 的传输数据到或从 Azure 文件 |Microsoft Docs
description: 使用 AzCopy 和文件存储的数据传输。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687929"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和文件存储传输数据 

AzCopy 是一个命令行实用工具，可用于向 / 从存储帐户复制 blob 或文件。 本文包含使用 Azure 文件的示例命令。

在开始之前，请参阅[开始使用 AzCopy](storage-use-azcopy-v10.md)一文，下载 AzCopy 并使自己熟悉的工具。

## <a name="create-file-shares"></a>创建文件共享

可以使用 AzCopy`make`命令以创建文件共享。 本部分中的示例创建名为的文件共享`myfileshare`。

|    |     |
|--------|-----------|
| **语法** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **示例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>上传文件

可以使用 AzCopy`copy`命令将上传文件和目录从本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 将文件上传通过使用通配符

> [!NOTE]
> AzCopy 不会自动计算并存储该文件的 md5 哈希代码。 如果你想要执行此操作的 AzCopy，然后追加`--put-md5`每个复制操作命令的标志。 这样一来，下载该文件后，AzCopy 计算的 MD5 哈希下载数据，并验证 MD5 哈希值存储在文件的`Content-md5`属性与计算的哈希匹配。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **示例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>上传目录

此示例将一个目录 （及其所有该目录中的文件） 复制到文件共享。 结果是相同的名称的文件共享中的目录。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

若要复制到文件共享中的目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

如果文件共享中指定的目录不存在的名称，AzCopy 将创建该名称的新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

你可以上载目录的内容，而不使用通配符 （*） 复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **示例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 追加`--recursive`标志，用于上传所有子目录中的文件。

## <a name="download-files"></a>下载文件

可以使用 AzCopy`copy`命令来下载文件、 目录和文件共享到本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 使用通配符字符下载文件

> [!NOTE]
> 如果`Content-md5`文件的属性值包含哈希值，AzCopy 计算下载的数据的 MD5 哈希，并验证 MD5 哈希值存储在文件的`Content-md5`属性与计算的哈希匹配。 如果这些值不匹配，除非通过追加重写此行为，则下载会失败`--check-md5=NoCheck`或`--check-md5=LogOnly`复制操作命令。

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

此示例生成一个名为目录`C:\myDirectory\myFileShareDirectory`，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

您可以下载目录的内容，而不使用通配符 （*） 复制包含目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **示例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> 追加`--recursive`标志，用于下载所有子目录中的文件。

## <a name="next-steps"></a>后续步骤

在以下这些文章中找到更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)