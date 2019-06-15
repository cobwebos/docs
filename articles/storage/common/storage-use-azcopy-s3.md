---
title: 数据传输到 Azure 存储从 Amazon S3 存储桶使用 AzCopy v10 |Microsoft Docs
description: 使用 AzCopy 和 Amazon S3 存储桶传输数据
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 414573a90e5a7b7de845b65e6e88715a26024f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125784"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>使用 AzCopy 将数据复制从 Amazon S3 存储桶

AzCopy 是一个命令行实用工具，可用于向 / 从存储帐户复制 blob 或文件。 本文可帮助你对象、 目录和存储桶将从 Amazon Web Services (AWS) S3 复制到 Azure blob 存储使用 AzCopy。

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择将提供授权凭据的方式

* 若要使用 Azure 存储的授权，请使用 Azure Active Directory (AD) 或共享访问签名 (SAS) 令牌。

* 若要使用 AWS S3 授权，请使用 AWS 访问密钥和机密访问密钥。

### <a name="authorize-with-azure-storage"></a>Azure 存储使用授权

请参阅[开始使用 AzCopy](storage-use-azcopy-v10.md)文章下载 AzCopy，并选择将提供到存储服务的授权凭据的方式。

> [!NOTE]
> 这篇文章中的示例假定你已通过你的身份使用身份验证`AzCopy login`命令。 AzCopy 然后使用你的 Azure AD 帐户授予对 Blob 存储中的数据访问权限。
>
> 如果你将使用 SAS 令牌来授予对 blob 数据的访问权限，可以将该令牌附加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`。

### <a name="authorize-with-aws-s3"></a>与 AWS S3 授权

收集你的 AWS 访问密钥和机密访问密钥，然后设置这些环境变量：

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>复制对象、 目录和存储桶

AzCopy 将使用[放置块从 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，因此直接 AWS S3 和存储服务器之间复制数据。 这些复制操作不使用您的计算机的网络带宽。

> [!IMPORTANT]
> 此功能目前处于预览状态。 如果您决定从 S3 存储桶中删除数据，复制操作之后，请务必验证数据已正确复制到存储帐户之前删除的数据。

### <a name="copy-an-object"></a>复制对象

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **示例** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> 在本文中的示例使用 AWS S3 存储桶路径样式 Url (例如： `http://s3.amazonaws.com/<bucket-name>`)。 
>
> 此外可以使用虚拟托管式 Url (例如： `http://bucket.s3.amazonaws.com`)。 
>
> 若要了解有关虚拟托管的存储桶的详细信息，请参阅 [虚拟托管的存储桶]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 。

### <a name="copy-a-directory"></a>复制目录

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **示例** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>复制存储桶

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **示例** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>在所有区域中复制所有存储桶

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **示例** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>在特定的 S3 区域复制所有存储桶

|    |     |
|--------|-----------|
| **语法** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **示例** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>处理对象命名规则之间的差异

AWS S3 具有一组不同的存储桶名称相比 Azure blob 容器的命名约定。 你可以阅读它们[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)。 如果您选择将存储桶中的组复制到 Azure 存储帐户，复制操作可能会由于命名差异失败。

AzCopy 处理两个最常见的问题可能会出现;包含时间段和包含连续的连字符存储桶的存储桶。 AWS S3 存储桶名称包含句点和连续的连字符，但不能在 Azure 中的容器。 AzCopy 会替换句点连字符和一个数字，表示连续的连字符数与连续的连字符 (例如： 名为的存储桶`my----bucket`变得`my-4-bucket`。 

此外，AzCopy 将复制的文件，因为它检查的命名冲突，并尝试解决这些问题。 例如，如果有同名的存储桶`bucket-name`并`bucket.name`，AzCopy 解析命名的存储桶`bucket.name`到第一个`bucket-name`然后`bucket-name-2`。

## <a name="handle-differences-in-object-metadata"></a>处理对象元数据中的差异

AWS S3 和 Azure 的对象键名称中允许不同的字符。 您可以阅读有关字符，使用 AWS S3[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)。 在 Azure 端，blob 的对象键遵循的命名规则[C#标识符](https://docs.microsoft.com/dotnet/csharp/language-reference/)。

AzCopy 的一部分`copy`命令时，可以提供一个值可选`s2s-invalid-metadata-handle`标志，指定你希望如何处理其中的文件的元数据包含不兼容的键名称的文件。 下表描述了每个标志值。

| 标记值 | 描述  |
|--------|-----------|
| **ExcludeIfInvalid** | （默认选项）元数据不包括在传输的对象。 AzCopy 记录警告。 |
| **FailIfInvalid** | 不会复制对象。 AzCopy 记录一个错误，并在传输摘要中显示的失败计数中包含该错误。  |
| **RenameIfInvalid**  | AzCopy 解析无效的元数据密钥，并将对象复制到 Azure 中使用已解析的元数据键/值对。 若要了解确切步骤 AzCopy 所需重命名的对象键，请参阅[如何 AzCopy 重命名的对象键](#rename-logic)下面一节。 如果 AzCopy 不能重命名项，不会复制的对象。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重命名的对象键

AzCopy 会执行以下步骤：

1. 替换无效字符 _。

2. 将字符串添加`rename_`到新的有效密钥的开头。

   此密钥将用于保存的原始元数据**值**。

3. 将字符串添加`rename_key_`到新的有效密钥的开头。
   此密钥将用于保存原始的元数据无效**密钥**。
   可以使用此密钥以尝试并恢复中 Azure 端的元数据，因为元数据密钥保留为 Blob 存储服务上的值。

## <a name="next-steps"></a>后续步骤

在以下这些文章中找到更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)