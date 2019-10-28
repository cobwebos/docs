---
title: 使用 AzCopy v10 将数据从 Amazon S3 存储桶传输到 Azure 存储 |Microsoft Docs
description: 用 AzCopy 和 Amazon S3 存储桶传输数据
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b984d194c75924451a52250490b1a5590b996974
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821376"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>使用 AzCopy 从 Amazon S3 存储桶复制数据

AzCopy 是一个命令行实用工具，可用于在存储帐户中复制 blob 或文件。 本文介绍如何使用 AzCopy 将对象、目录和存储桶从 Amazon Web Services （AWS） S3 复制到 Azure blob 存储。

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择如何提供授权凭据

* 若要使用 Azure 存储授权，请使用 Azure Active Directory （AD）或共享访问签名（SAS）令牌。

* 若要使用 AWS S3 进行授权，请使用 AWS 访问密钥和密钥访问密钥。

### <a name="authorize-with-azure-storage"></a>向 Azure 存储授权

请参阅[AzCopy 文章入门](storage-use-azcopy-v10.md)，下载 AzCopy，并选择如何向存储服务提供授权凭据。

> [!NOTE]
> 本文中的示例假设已使用 `AzCopy login` 命令对标识进行身份验证。 然后，AzCopy 使用 Azure AD 帐户来授权访问 Blob 存储中的数据。
>
> 如果你希望使用 SAS 令牌来授予对 blob 数据的访问权限，则可以将该令牌附加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`。

### <a name="authorize-with-aws-s3"></a>用 AWS S3 授权

收集 AWS 访问密钥和密钥访问密钥，然后设置以下环境变量：

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>复制对象、目录和存储桶

AzCopy 使用[URL API 中的 Put 块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)，因此，数据直接复制到 AWS S3 和存储服务器之间。 这些复制操作不会使用计算机的网络带宽。

> [!IMPORTANT]
> 此功能目前处于预览状态。 如果在执行复制操作后决定从 S3 存储桶中删除数据，请确保在删除数据之前，验证是否已将数据正确复制到存储帐户。

> [!TIP]
> 本节中的示例将路径参数括在单引号（' '）中。 在所有命令 shell 中使用单引号（Windows 命令行界面（cmd.exe）除外）。 如果使用的是 Windows 命令行界面（cmd.exe），请用双引号（""）而不是单引号（' '）将路径参数引起来。

### <a name="copy-an-object"></a>复制对象

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 本文中的示例使用 AWS S3 存储桶的路径样式 Url （例如： `http://s3.amazonaws.com/<bucket-name>`）。 
>
> 还可以使用虚拟托管样式的 Url （例如： `http://bucket.s3.amazonaws.com`）。 
>
> 若要了解有关存储桶的虚拟托管的详细信息，请参阅 [Bucket 的虚拟主机]] （ https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 。

### <a name="copy-a-directory"></a>复制目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>复制 bucket

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>复制所有区域中的所有存储桶

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **示例** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>复制特定 S3 区域中的所有存储桶

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **示例** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>处理对象命名规则中的差异

与 Azure blob 容器相比，AWS S3 具有一组不同的 bucket 名称命名约定。 可在[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)了解相关信息。 如果选择将一组存储桶复制到 Azure 存储帐户，则复制操作可能会因为命名差异而失败。

AzCopy 处理可能出现的两个最常见的问题;包含句点和包含连续连字符的存储桶的存储桶。 AWS S3 bucket 名称可以包含句点和连续的连字符，但 Azure 中的容器不能。 AzCopy 将句点替换为连字符，并将连续连字符替换为表示连续连字符数的数字（例如：名为 `my----bucket` 的存储桶将变为 `my-4-bucket`。 

此外，随着 AzCopy 副本的复制，它会检查命名冲突并尝试解决它们。 例如，如果存在名称为 `bucket-name` 和 `bucket.name`的存储桶，则 AzCopy 会将名为 `bucket.name` 的 bucket 首先解析为 `bucket-name`，然后解析为 `bucket-name-2`。

## <a name="handle-differences-in-object-metadata"></a>处理对象元数据的差异

AWS S3 和 Azure 允许对象键的名称中的字符集不同。 可在[此处](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)阅读有关 AWS S3 使用的字符的信息。 在 Azure 端，blob 对象键遵循[ C#标识符](https://docs.microsoft.com/dotnet/csharp/language-reference/)的命名规则。

作为 AzCopy `copy` 命令的一部分，你可以为可选的 `s2s-invalid-metadata-handle` 标志提供一个值，该值指定你希望如何处理文件的元数据包含不兼容密钥名称的文件。 下表描述了每个标志值。

| 标志值 | 描述  |
|--------|-----------|
| **ExcludeIfInvalid** | （默认选项）传输的对象中不包括元数据。 AzCopy 记录警告。 |
| **FailIfInvalid** | 不复制对象。 AzCopy 记录错误，并在传输摘要中出现失败计数中包含此错误。  |
| **RenameIfInvalid**  | AzCopy 解析无效的元数据密钥，并使用已解析的元数据密钥值对将对象复制到 Azure。 若要确切了解 AzCopy 重命名对象键的步骤，请参阅下面的 "[如何重命名对象密钥](#rename-logic)" 一节。 如果 AzCopy 无法重命名该密钥，则不会复制该对象。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重命名对象键

AzCopy 执行以下步骤：

1. 将无效字符替换为 "_"。

2. 将字符串 `rename_` 添加到新的有效键的开头。

   此密钥将用于保存原始元数据**值**。

3. 将字符串 `rename_key_` 添加到新的有效键的开头。
   此密钥将用于保存原始元数据无效**密钥**。
   你可以使用此密钥在 Azure 端尝试和恢复元数据，因为元数据密钥会保留为 Blob 存储服务中的值。

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)