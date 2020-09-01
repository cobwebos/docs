---
title: azcopy copy | Microsoft Docs
description: 本文提供有关 azcopy copy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b9d5a9e071cc1b2ac81e8cacea8c974181fbb3b6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070388"
---
# <a name="azcopy-copy"></a>azcopy copy

将源数据复制到目标位置。

## <a name="synopsis"></a>摘要

将源数据复制到目标位置。 支持的方向如下：

  - 本地 <-> Azure Blob（SAS 或 OAuth 身份验证）
  - 本地 <-> Azure 文件存储（共享/目录 SAS 身份验证）
  - 本地 <-> Azure Data Lake Storage Gen 2（SAS、OAuth 或共享密钥身份验证）
  - Azure Blob（SAS 或公共）-> Azure Blob（SAS 或 OAuth 身份验证）
  - Azure Blob（SAS 或公共）-> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure Blob（SAS 或 OAuth 身份验证）
  - Amazon Web Services (AWS) S3（访问密钥）-> Azure 块 Blob（SAS 或 OAuth 身份验证）

有关详细信息，请参阅本文的示例部分。

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="advanced"></a>高级

从本地磁盘上传时，AzCopy 将自动检测文件的内容类型。 AzCopy 会根据文件扩展名或内容（如果未指定扩展名）检测文件的内容类型。

内置的查找表较小，但在 Unix 上，已通过采用以下一个或多个名称的本地系统 `mime.types` 文件（如果可用）扩充了该查找表：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表提取的。 可以借助一个标志禁用此功能。 有关详细信息，请参阅本文中的标志部分。

如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑从命令行历史记录中清除包含凭据的变量。 若要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>示例

使用 OAuth 身份验证上传单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 `azcopy login` 命令。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
与前面的示例相同，但这一次此命令还会计算文件内容的 MD5 哈希，并将其另存为 Blob 的 Content-MD5 属性：

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

使用 SAS 令牌上传单个文件：

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 令牌和管道（仅限块 Blob）上传单个文件：
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

使用 SAS 令牌上传整个目录：
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

或

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

使用 SAS 令牌和通配符 (*) 上传一组文件：
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

使用 SAS 令牌和通配符 (*) 上传文件和目录：

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

使用 OAuth 身份验证下载单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 `azcopy login` 命令。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

使用 SAS 令牌下载单个文件：

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

使用 SAS 令牌下载单个文件，然后通过管道将输出传送到文件（仅限块 Blob）：
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

使用 SAS 令牌下载整个目录：
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

有关在 URL 中使用通配符 (*) 的说明：

仅支持通过两种方式在 URL 中使用通配符。 

- 紧接在 URL 的最后一个正斜杠 (/) 后面使用一个通配符。 通配符的这种用法会将目录中的所有文件直接复制到目标，而不会将其放入子目录。 

- 也可以在容器名称中使用通配符，前提是 URL 只引用容器而不引用 Blob。 可以使用此方法从一部分容器中获取文件。 

下载目录的内容，而不复制包含的目录本身。
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

下载整个存储帐户。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

在容器名称中使用通配符 (*)，下载存储帐户中的一部分容器。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

使用 SAS 令牌将单个 Blob 复制到另一个 Blob。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 令牌和身份验证令牌将单个 Blob 复制到另一个 Blob。 必须在源帐户 URL 的末尾使用 SAS 令牌，但如果使用 `azcopy login` 命令登录到 AzCopy，则目标帐户不需要 SAS 令牌。 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

使用 SAS 令牌将一个 Blob 虚拟目录复制到另一个 Blob：

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

使用 SAS 令牌将一个存储帐户中的所有 Blob 容器、目录和 Blob 复制到另一个存储帐户：

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

使用访问密钥和 SAS 令牌将单个对象复制到 Amazon Web Services (AWS) S3 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY`。
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用访问密钥和 SAS 令牌将整个目录复制到 AWS S3 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY`。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  请参阅 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 以更好地了解 [folder] 占位符。

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY`。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 区域中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY`。
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

在桶名称中使用通配符 (*) 来复制一部分桶。 如前面的示例所示，需要提供访问密钥和 SAS 令牌。 请确保为 AWS S3 源设置环境变量 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY`。

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>选项

**--backup** - 激活 Windows 用于上传的 SeBackupPrivilege 或用于下载的 SeRestorePrivilege，以允许 AzCopy 查看和读取所有文件（无论其文件系统权限如何），并恢复所有权限。 要求运行 AzCopy 的帐户已经具有这些权限（例如，拥有管理员权限，或者是 `Backup Operators` 组的成员）。 此标志激活帐户已经具有的权限。

**--blob-type** 字符串  定义目标中的 Blob 类型。 此选项用于上传 Blob 以及在帐户之间进行复制（默认值为 `Detect`）。 有效值包括 `Detect`、 `BlockBlob`、 `PageBlob`和 `AppendBlob`。 在帐户之间复制时，使用值 `Detect` 会导致 AzCopy 使用源 Blob 的类型来确定目标 Blob 的类型。 上传文件时，`Detect` 会根据文件扩展名确定文件是 VHD 文件还是 VHDX 文件。 如果文件是 VHD 或 VHDX 文件，则 AzCopy 会将该文件视为页 Blob。 （默认值为“Detect”）

**--block-blob-tier** 字符串 - 使用此 blob 层将块 blob 上传到 Azure 存储。 （默认值为“None”）

**--block-size-mb** 浮点数 - 在上传到 Azure 存储以及从 Azure 存储下载时使用此块大小（以 MiB 为单位）。 默认值是根据文件大小自动计算的。 允许使用小数（例如：0.25）。

**--cache-control** 字符串 - 设置 cache-control 标头。 下载时返回。

**--check-length** - 传输后检查目标上的文件长度。 如果源与目标之间不匹配，则将传输标记为失败。 （默认值为 `true`）。

**--check-md5** 字符串 - 指定下载时验证 MD5 哈希的严格程度。 仅在下载时可用。 可用选项：`NoCheck`、`LogOnly`、`FailIfDifferent``FailIfDifferentOrMissing`。 （默认值为 `FailIfDifferent`）。

**--content-disposition** 字符串 - 设置 content-disposition 标头。 下载时返回。

**--content-encoding** 字符串 - 设置 content-encoding 标头。 下载时返回。

**--content-language** 字符串 - 设置 content-language 标头。 下载时返回。

**--content-type** 字符串 - 指定文件的内容类型。 暗指 no-guess-mime-type。 下载时返回。

**--decompress** - 下载时自动解压缩文件（如果 content-encoding 指示文件已压缩）。 支持的 content-encoding 值为 `gzip` 和 `deflate`。 不需要提供 `.gz`/`.gzip`或 `.zz` 文件扩展名，但如果存在，则将其删除。

**--exclude-attributes** 字符串 -（仅限 Windows）排除其属性与属性列表相匹配的文件。 例如：A;S;R

**--exclude-blob-type** 字符串 -（可选）指定从容器或帐户复制 Blob 时要排除的 Blob 类型 (`BlockBlob`/ `PageBlob`/ `AppendBlob`)。 此标志不适用于将数据从非 Azure 服务复制到 Azure 服务。 应使用 `;` 分隔多个 Blob。 

**--exclude-path** 字符串 - 复制时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如：`myFolder;myFolder/subDirName/file.pdf`）。 与帐户遍历结合使用时，路径不包含容器名称。

**--exclude-pattern** 字符串 - 复制时排除这些文件。 此选项支持通配符 (*)。

**--follow-symlinks** - 从本地文件系统上传时遵循符号链接。

**--force-if-read-only** - 在 Windows 或 Azure 文件上覆盖现有文件时，即使现有文件已设置只读属性，也要执行覆盖操作。

**--from-to** 字符串 -（可选）指定源目标组合。 例如：`LocalBlob`、`BlobLocal`、`LocalBlobFS`。

 - copy 命令的帮助。

**--include-after** 字符串 - 只包括在给定日期/时间或之后修改的文件。 该值应为 ISO8601 格式。 如果未指定时区，则假定该值位于运行 AzCopy 的计算机的本地时区中。 例如，`2020-08-19T15:04:00Z` 表示 UTC 时间，`2020-08-19` 表示本地时区的午夜 (00:00)。 与 AzCopy 10.5 一样，此标志仅适用于文件，不适用于文件夹，因此当将此标志与 `--preserve-smb-info` 或 `--preserve-smb-permissions` 一起使用时，将不会复制文件夹属性。

**--include-attributes** 字符串 -（仅限 Windows）包括其属性与属性列表相匹配的文件。 例如：A;S;R

**--include-path** 字符串 - 复制时仅包括这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如：`myFolder;myFolder/subDirName/file.pdf`）。

**--include-pattern** 字符串 - 复制时仅包括这些文件。 此选项支持通配符 (*)。 使用 `;` 分隔文件。

**--版本列表** 字符串指定一个文件，其中每个版本 id 都在单独的行上列出。 确保源必须指向单个 blob，并且使用此标志在文件中指定的所有版本 id 必须仅属于源 blob。 AzCopy 将下载提供的目标文件夹中的指定版本。 有关详细信息，请参阅 [下载以前版本的 blob](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob)。

**--log-level** 字符串 - 定义日志文件的日志详细程度，可用级别：INFO（所有请求/响应）、WARNING（响应缓慢）、ERROR（仅限失败的请求）和 NONE（无输出日志）。 （默认值为 `INFO`）。 

**--metadata** 字符串 - 将这些键值对作为元数据上传到 Azure 存储。

**--no-guess-mime-type** - 阻止 AzCopy 根据文件的扩展名或内容来检测内容类型。

**--overwrite** 字符串 - 如果此标志设置为 true，则会覆盖目标上有冲突的文件和 Blob。 （默认值为 `true`）可能的值包括：`true`、`false`、`prompt` 和 `ifSourceNewer`。 对于支持文件夹的目标，冲突的文件夹级别属性将被覆盖，此标志为 `true` 或对提示提供肯定的响应。 （默认值为“true”）

**--page-blob-tier** 字符串 - 使用此 blob 层将页 blob 上传到 Azure 存储。 （默认值为 `None`）。 （默认值为“None”）

**--preserve-last-modified-time** - 仅当目标为文件系统时才可用。

**--preserve-owner** - 仅当下载时，且仅当使用 `--preserve-smb-permissions` 时才起作用。 如果为 true（默认值），则下载内容中将保留文件“所有者”和“组”。 如果设置为 false，则 `--preserve-smb-permissions` 仍保留 ACL，但“所有者”和“组”的保留将基于运行 AzCopy 的用户（默认值为 true）。

**--preserve-smb-info** - 默认值为 False。 保留 SMB 感知资源（Windows 和 Azure 文件存储）之间的 SMB 属性信息（上次写入时间、创建时间、属性位）。 只会传输 Azure 文件存储支持的属性位；其他的将被忽略。 此标志同时适用于文件和文件夹，除非指定了“仅文件”筛选器（例如包含模式）。 为文件夹传输的信息与为文件传输的信息几乎相同，只是“上次写入时间”除外，不会为文件夹保留该信息。

**--preserve-smb-permissions** - 默认值为 False。 保留感知资源（Windows 和 Azure 文件存储）之间的 SMB ACl。 对于下载操作，还需要使用 `--backup` 标志来恢复权限，其中新所有者将不是运行 AzCopy 的用户。 此标志同时适用于文件和文件夹，除非指定了“仅文件”筛选器（例如 `include-pattern`）。

**--put-md5** - 创建每个文件的 MD5 哈希，并将该哈希另存为目标 Blob 或文件的 Content-MD5 属性。 （默认不会创建哈希。）仅在上传时可用。

**--recursive** - 从本地文件系统上传时以递归方式检查子目录。

**--s2s-detect-source-changed** - 检测源文件/blob 在读取时是否发生更改。 （此参数仅适用于服务到服务的副本，因为将对上传和下载永久启用相应的检查。）

**--s2s-handle-invalid-metadata** 字符串   指定如何处理无效的元数据键。 可用选项：ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。 （默认值为 `ExcludeIfInvalid`）。 （默认值为“ExcludeIfInvalid”）

**--s2s-preserve-access-tier** - 在服务之间复制过程中保留访问层 请参阅 [Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，确保目标存储帐户支持设置访问层。 如果不支持设置访问层，请使用 s2sPreserveAccessTier=false 来绕过访问层的复制。 （默认值为 true）。  （默认值为 true）

**--s2s-preserve-properties** - 在服务之间复制过程中保留完整属性。 对于 AWS S3 和 Azure 文件存储的非单一文件源，列出操作不会返回对象和文件的完整属性。 若要保留完整属性，AzCopy 需要对每个对象或文件发送一个附加的请求。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps 浮点数** - 以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--output-type** 字符串   命令输出的格式。 选项包括：text、json。 默认值为 `text`。 （默认值为“text”）

--trusted-microsoft-suffixes 字符串指定可向其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认为 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
