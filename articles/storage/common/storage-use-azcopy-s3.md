---
title: 使用 AzCopy 将数据从 Amazon S3 复制到 Azure 存储 | Microsoft Docs
description: 使用 AzCopy 和 Amazon S3 桶传输数据
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 7f6d2be3760a28a8702b221dbd484901918441d9
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195208"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>使用 AzCopy 将数据从 Amazon S3 复制到 Azure 存储

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文介绍如何使用 AzCopy 将对象、目录和桶从 Amazon Web Services (AWS) S3 复制到 Azure Blob 存储。

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择如何提供授权凭据

* 若要对 Azure 存储授权，请使用 Azure Active Directory (AD) 或共享访问签名 (SAS) 令牌。

* 若要对 AWS S3 授权，请使用 AWS 访问密钥和机密访问密钥。

### <a name="authorize-with-azure-storage"></a>对 Azure 存储授权

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并选择如何提供存储服务的授权凭据。

> [!NOTE]
> 本文中的示例假设你已使用 `AzCopy login` 命令验证自己的身份。 然后，AzCopy 会使用你的 Azure AD 帐户来授权访问 Blob 存储中的数据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`。

### <a name="authorize-with-aws-s3"></a>对 AWS S3 授权

收集 AWS 访问密钥和机密访问密钥，然后设置以下环境变量：

| 操作系统 | Command  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>复制对象、目录和桶

AzCopy 使用[从 URL 放置块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，因此数据将在 AWS S3 与存储服务器之间直接复制。 这些复制操作不会占用计算机的网络带宽。

> [!IMPORTANT]
> 此功能目前处于预览状态。 如果你决定在完成复制操作后从 S3 桶中删除数据，请务必在删除数据之前，确认是否已将数据正确复制到存储帐户。

> [!TIP]
> 本部分中的示例将路径参数括在单引号 ('') 中。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

 这些示例也适用于具有分层命名空间的帐户。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md)使你可以在这些帐户上使用相同的 URL 语法 (`blob.core.windows.net`)。 

### <a name="copy-an-object"></a>复制对象

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **示例**（分层命名空间） | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 本文中的示例对 AWS S3 桶使用路径样式的 URL（例如：`http://s3.amazonaws.com/<bucket-name>`）。 
>
> 也可以使用虚拟托管样式的 URL（例如：`http://bucket.s3.amazonaws.com`）。 
>
> 若要详细了解桶的虚拟托管，请参阅 [桶的虚拟托管]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 。

### <a name="copy-a-directory"></a>复制目录

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **示例**（分层命名空间）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>复制桶

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **示例**（分层命名空间）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>复制所有区域中的所有桶

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **示例**（分层命名空间）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>复制特定 S3 区域中的所有桶

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **示例** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **示例**（分层命名空间）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>处理对象命名规则的差异

相比 Azure Blob 容器，AWS S3 对桶的名称实施一组不同的命名约定。 可在[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)了解相关信息。 如果选择将一组桶复制到 Azure 存储帐户，复制操作可能因命名差异而失败。

AzCopy 会处理可能出现的两个最常见问题：包含句点的桶，以及包含连续连字符的桶。 AWS S3 桶名称可以包含句点和连续的连字符，但 Azure 中的容器则不可以。 AzCopy 会将句点替换为连字符，并将连续的连字符替换为表示连续连字符数目的数字（例如：名为 `my----bucket` 的桶将变成 `my-4-bucket`）。 

此外，在 AzCopy 复制文件时，它会检查并尝试解决命名冲突。 例如，如果存在名为 `bucket-name` 和 `bucket.name` 的桶，则 AzCopy 会将名为 `bucket.name` 的桶解析为 `bucket-name`，再将后者解析为 `bucket-name-2`。

## <a name="handle-differences-in-object-metadata"></a>处理对象元数据的差异

AWS S3 和 Azure 允许在对象键名称中使用不同的字符集。 可在[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)了解 AWS S3 使用的字符。 在 Azure 端，Blob 对象键遵守 [C# 标识符](https://docs.microsoft.com/dotnet/csharp/language-reference/)的命名规则。

在 AzCopy `copy` 命令中，可为 `s2s-invalid-metadata-handle` 可选标志提供一个值，用于指定如何处理其中的元数据包含不兼容键名称的文件。 下表描述了每个标志值。

| 标志值 | 说明  |
|--------|-----------|
| **ExcludeIfInvalid** | （默认选项）不在传输的对象中包含元数据。 AzCopy 将记录警告。 |
| **FailIfInvalid** | 不复制对象。 AzCopy 将记录错误，并将该错误包含到传输摘要显示的失败计数中。  |
| **RenameIfInvalid**  | AzCopy 将解析无效的元数据键，并使用已解析的元数据键值对将对象复制到 Azure。 若要确切地了解 AzCopy 采取哪些步骤来重命名对象键，请参阅下面的 [AzCopy 如何重命名对象键](#rename-logic)部分。 如果 AzCopy 无法重命名该键，则不会复制该对象。 |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重命名对象键

AzCopy 执行以下步骤：

1. 将无效字符替换为“_”。

2. 将字符串 `rename_` 添加到新的有效键的开头。

   此键将用于保存原始元数据的**值**。

3. 将字符串 `rename_key_` 添加到新的有效键的开头。
   此键将用于保存原始元数据的无效**键**。
   可以使用此键在 Azure 端尝试恢复元数据，因为元数据键作为值保留在 Blob 存储服务中。

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)