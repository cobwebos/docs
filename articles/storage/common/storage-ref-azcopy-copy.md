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
ms.openlocfilehash: 883d0afac5623838e9dde068964b36cfe3b44380
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281984"
---
# <a name="azcopy-copy"></a>azcopy copy

将源数据复制到目标位置。

## <a name="synopsis"></a>摘要

将源数据复制到目标位置。 支持的方向如下：

  - 本地 <-> Azure Blob（SAS 或 OAuth 身份验证）
  - 本地 <-> Azure 文件存储（共享/目录 SAS 身份验证）
  - local <-> Azure Data Lake Storage 第2代（SAS、OAuth 或共享密钥身份验证）
  - Azure Blob（SAS 或公共）-> Azure Blob（SAS 或 OAuth 身份验证）
  - Azure Blob（SAS 或公共）-> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure Blob（SAS 或 OAuth 身份验证）
  - Amazon Web Services （AWS） S3 （访问密钥）-> Azure 块 Blob （SAS 或 OAuth 身份验证）

有关详细信息，请参阅本文的 "示例" 部分。

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="advanced"></a>高级

当你从本地磁盘上传文件时，AzCopy 将自动检测文件的内容类型。 AzCopy 根据文件扩展名或内容（如果未指定扩展名）检测内容类型。

内置查找表很小，但在 Unix 上，如果本地系统的 `mime.types` 文件可用在以下一个或多个名称中，则会将其扩充：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表提取的。 可以借助一个标志禁用此功能。 有关详细信息，请参阅本文的标志部分。

如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>示例

使用 OAuth 身份验证上传单个文件。 如果尚未登录到 AzCopy，请运行 `azcopy login` 以下命令，然后运行命令。

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

使用 OAuth 身份验证下载单个文件。 如果尚未登录到 AzCopy，请运行 `azcopy login` 以下命令，然后运行命令。

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

- 紧接在 URL 的最后一个正斜杠 (/) 后面使用一个通配符。 使用通配符将目录中的所有文件直接复制到目标，而不将其放入子目录。 

- 你还可以在容器名称中使用通配符，只要 URL 只引用容器而不是 blob。 可以使用此方法从一部分容器中获取文件。 

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

使用 SAS 令牌和身份验证令牌将单个 blob 复制到另一个 blob。 你必须在源帐户 URL 末尾使用 SAS 令牌，但如果你使用命令登录到 AzCopy，则目标帐户不需要 `azcopy login` 。 

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

使用访问密钥和 SAS 令牌将单个对象复制到 Amazon Web Services (AWS) S3 中的 Blob 存储。 首先， `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` 为 AWS S3 源设置环境变量和。
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用访问密钥和 SAS 令牌将整个目录复制到 AWS S3 中的 Blob 存储。 首先， `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` 为 AWS S3 源设置环境变量和。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  请参阅以 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 更好地了解 "[文件夹]" 占位符。

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 中的 Blob 存储。 首先， `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` 为 AWS S3 源设置环境变量和。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 区域中的 Blob 存储。 首先， `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` 为 AWS S3 源设置环境变量和。
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

在桶名称中使用通配符 (*) 来复制一部分桶。 如前面的示例所示，需要提供访问密钥和 SAS 令牌。 请确保为 `AWS_ACCESS_KEY_ID` AWS S3 源设置环境变量和 `AWS_SECRET_ACCESS_KEY` 。

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>选项

**--备份**激活 Windows "SeBackupPrivilege for 上传" 或 "用于下载的 SeRestorePrivilege"，以允许 AzCopy 查看和读取所有文件，不管文件系统权限如何，还可以还原所有权限。 要求运行 AzCopy 的帐户已经具有这些权限（例如，拥有管理员权限，或者是组的成员 `Backup Operators` ）。 此标记激活帐户已有的权限。

**--blob-type** 字符串  定义目标中的 Blob 类型。 这用于上传 blob 以及在帐户之间复制（默认值 `Detect` ）。 有效值包括 `Detect`、 `BlockBlob`、 `PageBlob`和 `AppendBlob`。 在帐户之间复制时，值 `Detect` 会导致 AzCopy 使用源 blob 的类型来确定目标 blob 的类型。 上传文件时， `Detect` 确定该文件是基于文件扩展名的 VHD 还是 VHDX 文件。 如果文件是 VHD 或 VHDX 文件，则 AzCopy 会将该文件视为页 Blob。 （默认值为“Detect”）

**--块 blob 层**字符串上传将块 blob 用于使用此 blob 层的 Azure 存储。 （默认值为“None”）

**--块大小-mb**浮点：在上传到 azure 存储时使用此块大小（在 MiB 中指定），并从 Azure 存储空间下载。 默认值是根据文件大小自动计算的。 允许使用小数（例如：0.25）。

**--cache 控制**字符串设置缓存控制标头。 下载时返回。

**--检查长度**传输后检查目标上的文件的长度。 如果源与目标之间不匹配，则将传输标记为失败。 （默认值为 `true` ）

**--check-md5** string 指定在下载时应如何验证严格的 md5 哈希。 仅在下载时可用。 可用选项： `NoCheck` 、 `LogOnly` 、 `FailIfDifferent` 和 `FailIfDifferentOrMissing` 。 （默认值 `FailIfDifferent` ）（默认值为 "FailIfDifferent"）

**--内容处置**字符串设置内容处置标头。 下载时返回。

**--content 编码**字符串设置内容编码标头。 下载时返回。

**--content-language** string 设置 content-type 标头。 下载时返回。

**--content-type** string 指定文件的内容类型。 暗指 no-guess-mime-type。 下载时返回。

**--解压缩**下载文件时自动解压缩文件（如果其内容编码表明它们已压缩）。 支持的内容编码值为 `gzip` 和 `deflate` 。 或的文件扩展名 `.gz` / `.gzip` 不是 `.zz` 必需的，但如果存在，则将其删除。

**--exclude-** attribute string （仅限 Windows）排除其属性与属性列表匹配的文件。 例如：A;S;R

**--exclude-类型**字符串（可选 `BlockBlob` /  `PageBlob` /  `AppendBlob` ）在从容器或帐户复制 blob 时，指定要排除的 blob 类型。 使用此标志不适用于将数据从非 Azure 服务复制到服务。 应将多个 blob 分隔开来 `;` 。 

**--排除-路径**字符串在复制时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如： `myFolder;myFolder/subDirName/file.pdf` ）。 与帐户遍历结合使用时，路径不包含容器名称。

**--exclude**在复制时排除这些文件。 此选项支持通配符 (*)。

**--符号链接** 从本地文件系统上传时，请遵循符号链接。

**--force-只读**覆盖 Windows 或 Azure 文件上的现有文件时，即使现有文件已设置为只读属性，也强制覆盖工作。

**--从-到**字符串（可选）指定源目标组合。 例如： `LocalBlob` 、 `BlobLocal` 、 `LocalBlobFS` 。

**--帮助**帮助进行复制。

**--include-后面**仅包含在给定日期/时间或之后修改的文件。 该值应为 ISO8601 格式。 如果未指定时区，则假定值为运行 AzCopy 的计算机的本地时区。 例如， `2020-08-19T15:04:00Z` 对于 UTC 时间，或 `2020-08-19` 在本地时区的午夜（00:00）。 与 AzCopy 10.5 一样，此标志仅适用于文件，而不适用于文件夹，因此，在将此标志与或一起使用时，不会复制文件夹属性 `--preserve-smb-info` `--preserve-smb-permissions` 。

**--include-** attribute string （仅限 Windows）包括其属性与属性列表匹配的文件。 例如：A;S;R

**--include-path**字符串在复制时仅包含这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如： `myFolder;myFolder/subDirName/file.pdf` ）。

**--include-模式**字符串在复制时仅包含这些文件。 此选项支持通配符 (*)。 使用单独的文件 `;` 。

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值 `INFO` ）。 

**--** 将这些键值对作为元数据上传到 Azure 存储的元数据字符串。

**--非推测-mime 类型** 阻止 AzCopy 根据文件的扩展名或内容来检测内容类型。

**--** 如果此标志设置为 true，则覆盖字符串将覆盖目标上的冲突文件和 blob。 （默认值 `true` ）可能的值包括 `true` 、、 `false` `prompt` 和 `ifSourceNewer` 。 对于支持文件夹的目标，将覆盖冲突的文件夹级别属性。此标志为， `true` 或者如果向提示提供肯定响应，则为。 （默认值为“true”）

**--页 blob 层**字符串使用此 blob 层将页 Blob 上传到 Azure 存储。 （默认值 `None` ）。 （默认值为“None”）

**--保留-上次修改时间** 仅当 destination 为文件系统时可用。

**--保留-所有者**   只有在使用时才会对下载产生影响 `--preserve-smb-permissions` 。 如果为 true（默认值），则下载内容中将保留文件“所有者”和“组”。 如果设置为 false， `--preserve-smb-permissions` 将仍保留 acl，但所有者和组将基于运行 AzCopy 的用户（默认值为 true）

**--保留-smb-信息**  默认值为 False。 保留 SMB 感知资源（Windows 和 Azure 文件存储）之间的 SMB 属性信息（上次写入时间、创建时间、属性位）。 只会传输 Azure 文件存储支持的属性位；其他的将被忽略。 此标志适用于文件和文件夹，除非指定了 "仅文件" 筛选器（例如，包含模式）。 为文件夹传输的信息与用于文件的信息相同，但上次写入时间除外，文件夹的上次写入时间除外。

**--保留-smb 权限**  默认值为 False。 保留感知资源（Windows 和 Azure 文件存储）之间的 SMB ACl。 对于下载，你还需要 `--backup` 标志来还原权限，其中新所有者不是运行 AzCopy 的用户。 此标志适用于文件和文件夹，除非指定了 "仅文件" 筛选器（例如 `include-pattern` ）。

**--put-md5**   创建每个文件的 MD5 哈希，并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 （默认不会创建哈希。）仅在上传时可用。

**--recursive**   从本地文件系统上传时，以递归方式查看子目录。

**--s2s-已更改**  检测文件/blob 在读取时是否发生更改。 （此参数仅适用于服务到服务副本，因为将为上传和下载永久启用相应的检查。）

**--s2s-handle-invalid-metadata** 字符串   指定如何处理无效的元数据键。 可用选项：ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。 （默认值 `ExcludeIfInvalid` ）。 （默认值为“ExcludeIfInvalid”）

**--s2s-保留-访问层**  服务间复制时保留访问层。 请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，确保目标存储帐户支持设置访问层。 如果不支持设置访问层，请使用 s2sPreserveAccessTier = false 来绕过复制访问层。 （默认值为 true）。  （默认值为 true）

**--s2s-保留-属性**  在服务间复制服务时保留完整的属性。 对于 AWS S3 和 Azure 文件存储的非单一文件源，列出操作不会返回对象和文件的完整属性。 若要保留完整属性，AzCopy 需要对每个对象或文件发送一个附加的请求。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps float**  以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--output-type** 字符串   命令输出的格式。 选项包括：text、json。 默认值为 `text`。 （默认值为“text”）

--trusted-microsoft-suffixes 字符串指定可向其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
