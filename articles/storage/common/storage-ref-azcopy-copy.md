---
title: azcopy copy | Microsoft Docs
description: 本文提供有关 azcopy copy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253333"
---
# <a name="azcopy-copy"></a>azcopy copy

将源数据复制到目标位置。

## <a name="synopsis"></a>摘要

将源数据复制到目标位置。 支持的方向如下：

  - 本地 <-> Azure Blob（SAS 或 OAuth 身份验证）
  - 本地 <-> Azure 文件存储（共享/目录 SAS 身份验证）
  - 本地 <-> ADLS Gen 2（SAS、OAuth 或共享密钥身份验证）
  - Azure Blob（SAS 或公共）-> Azure Blob（SAS 或 OAuth 身份验证）
  - Azure Blob（SAS 或公共）-> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure 文件存储 (SAS)
  - Azure 文件存储 (SAS) -> Azure Blob（SAS 或 OAuth 身份验证）
  - AWS S3（访问密钥）-> Azure 块 Blob（SAS 或 OAuth 身份验证）

有关详细信息，请参阅示例。

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="advanced"></a>高级

从本地磁盘上传时，AzCopy 会根据文件扩展名或内容（如果未指定扩展名）自动检测文件的内容类型。

内置的查找表较小，但在 Unix 上，已通过采用以下一个或多个名称的本地系统 mime.types 文件（如果可用）扩充了该查找表：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表提取的。 可以借助一个标志禁用此功能。 请参阅“标志”部分。

如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑从命令行历史记录中清除包含凭据的变量。 若要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>示例

使用 OAuth 身份验证上传单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 azcopy login 命令。

- azcopy cp "/path/to/file.txt" "https：//[account]/[容器]/[path/to/blob]"

与前面的示例相同，但这一次此命令还会计算文件内容的 MD5 哈希，并将其另存为 Blob 的 Content-MD5 属性：

- azcopy cp "/path/to/file.txt" "https：//[account]/[容器]/[path/to/blob]"--put-md5

使用 SAS 令牌上传单个文件：

- azcopy cp "/path/to/file.txt" "https：//[account]/[container]/[path/to/blob]？[SAS] "

使用 SAS 令牌和管道（仅限块 Blob）上传单个文件：
  
- cat "/path/to/file.txt" |azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] "

使用 SAS 令牌上传整个目录：
  
- azcopy cp "/path/to/dir" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true

或

- azcopy cp "/path/to/dir" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true--put-md5

使用 SAS 令牌和通配符 (*) 上传一组文件：

- azcopy cp "/path/*foo/* bar/* .pdf" "https：//[account]/[container]/[path/to/directory]？[SAS] "

使用 SAS 令牌和通配符 (*) 上传文件和目录：

- azcopy cp "/path/*foo/* bar *" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true

使用 OAuth 身份验证下载单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 azcopy login 命令。

- azcopy cp "https：//[account]/path/to/file.txt/[容器]/[path/to/blob]" ""

使用 SAS 令牌下载单个文件：

- azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] ""/path/to/file.txt "

使用 SAS 令牌下载单个文件，然后通过管道将输出传送到文件（仅限块 Blob）：
  
- azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] ">"/path/to/file.txt "

使用 SAS 令牌下载整个目录：
  
- azcopy cp "https：//[account]. .net/[容器]/[路径/to/directory]？[SAS] ""/path/to/dir "--recursive = true

有关在 URL 中使用通配符 (*) 的说明：

仅支持通过两种方式在 URL 中使用通配符。 

- 紧接在 URL 的最后一个正斜杠 (/) 后面使用一个通配符。 这会将目录中的所有文件直接复制到目标，而不会将其放入子目录。

- 也可以在容器名称中使用一个通配符，前提是 URL 只引用容器而不引用 Blob。 可以使用此方法从一部分容器中获取文件。

下载目录的内容，而不复制包含的目录本身。

- azcopy cp "https：//[srcaccount]. .net/[容器]/[path/to/folder]/*？[SAS] ""/path/to/dir "

下载整个存储帐户。

- azcopy cp "https：//[srcaccount]/" "/path/to/dir"--recursive

在容器名称中使用通配符 (*)，下载存储帐户中的一部分容器。

- azcopy cp "https：//[srcaccount]/[容器 * 名称]" "/path/to/dir"--递归

使用 SAS 令牌将单个 Blob 复制到另一个 Blob。

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/到/blob]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/到/blob]？[SAS] "

使用 SAS 令牌和 OAuth 令牌将单个 Blob 复制到另一个 Blob。 必须在源帐户 URL 的末尾使用 SAS 令牌，但如果使用 azcopy login 命令登录到 AzCopy，则目标帐户不需要 SAS 令牌。 

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/到/blob]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/到/blob]"

使用 SAS 令牌将一个 Blob 虚拟目录复制到另一个 Blob：

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/to/directory]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/to/directory]？[SAS] "--recursive = true

使用 SAS 令牌将一个存储帐户中的所有 Blob 容器、目录和 Blob 复制到另一个存储帐户：

- azcopy cp "https：//[srcaccount][SAS] "" https：//[destaccount][SAS] "--recursive = true

使用访问密钥和 SAS 令牌将单个对象复制到 Amazon Web Services (AWS) S3 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。
  
- azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https：//[destaccount]/[container]/[path/to/blob]？[SAS] "

使用访问密钥和 SAS 令牌将整个目录复制到 AWS S3 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https：//[destaccount]/[container]/[path/to/directory]？[SAS] "--recursive = true

请参阅 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 以更好地了解 [folder] 占位符。

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/" "https：//[destaccount][SAS] "--recursive = true

使用访问密钥和 SAS 令牌将所有桶复制到 Amazon Web Services (AWS) 区域中的 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3-[region]. amazonaws/" "https：//[destaccount]。[SAS] "--recursive = true

在桶名称中使用通配符 (*) 来复制一部分桶。 如前面的示例所示，需要提供访问密钥和 SAS 令牌。 请确保为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/[bucket * name]/" "https：//[destaccount][SAS] "--recursive = true

## <a name="options"></a>选项

**--备份**                              激活 Windows "SeBackupPrivilege for 上传" 或 "用于下载的 SeRestorePrivilege"，以允许 AzCopy 查看读取所有文件，不管文件系统权限如何，并还原所有权限。 要求运行 AzCopy 的帐户已经具有这些权限（例如，拥有管理员权限，或者是 "Backup Operators" 组的成员）。 此标志的所有功能都是该帐户已经具有的 "激活" 特权。

**--blob-type** 字符串                     定义目标中的 Blob 类型。 此选项用于上传 Blob 以及在帐户之间进行复制（默认值为“Detect”）。 有效值包括“Detect”、“BlockBlob”、“PageBlob”和“AppendBlob”。 在帐户之间复制时，使用值“Detect”会导致 AzCopy 使用源 Blob 的类型来确定目标 Blob 的类型。 上传文件时，“Detect”会根据文件扩展名确定文件是 VHD 文件还是 VHDX 文件。 如果文件是 VHD 或 VHDX 文件，则 AzCopy 会将该文件视为页 Blob。 （默认值为“Detect”）

**--块 blob-** 将块 Blob 直接上传到所选的[访问层](../blobs/storage-blob-storage-tiers.md)。 （默认值为 "无"）。 有效值包括 "无"、"热"、"冷" 和 "存档"。 如果未传递 "无" 或任何层，则 blob 将继承存储帐户的层。

**--block-size-mb** 浮点数                  在上传到 Azure 存储以及从 Azure 存储下载时使用此块大小（以 MiB 为单位）。 默认值是根据文件大小自动计算的。 允许使用小数小数（例如：0.25）。

**--cache-control** 字符串                 设置 cache-control 标头。 下载时返回。

**--check-length**                         传输后检查目标上的文件长度。 如果源与目标之间不匹配，则将传输标记为失败。 （默认值为 true）

**--check-md5** 字符串                     指定下载时验证 MD5 哈希的严格程度。 仅在下载时可用。 可用选项： NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 （默认值为“FailIfDifferent”）

**--content-disposition** 字符串           设置 content-disposition 标头。 下载时返回。

**--content-encoding** 字符串              设置 content-encoding 标头。 下载时返回。

**--content-language** 字符串              设置 content-language 标头。 下载时返回。

**--content-type** 字符串                  指定文件的内容类型。 暗指 no-guess-mime-type。 下载时返回。

**--decompress**                           下载时自动解压缩文件（如果 content-encoding 指示文件已压缩）。 支持的 content-encoding 值为“gzip”和“deflate”。 不需要提供“.gz”/“.gzip”或“.zz”文件扩展名，但如果存在，会将其删除。

**--exclude-attributes** 字符串            （仅限 Windows）排除其属性与属性列表相匹配的文件。 例如： A;些迅驰

**--exclude-blob-type** 字符串             （可选）指定从容器或帐户复制 Blob 时要排除的 Blob 类型 (BlockBlob/PageBlob/AppendBlob)。 此标志不适用于将数据从非 Azure 服务复制到 Azure 服务。 应使用“;”分隔多个 Blob。

**--exclude-path** 字符串                  复制时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如：myFolder;myFolder/subDirName/file.pdf）。 与帐户遍历结合使用时，路径不包含容器名称。

**--exclude-pattern** 字符串               复制时排除这些文件。 此选项支持通配符 (*)

**--follow-symlinks**                      从本地文件系统上传时遵循符号链接。

**--from-to** 字符串                       （可选）指定源-目标组合。 例如： LocalBlob、BlobLocal、LocalBlobFS。

**-h、--help**                                 copy 命令的帮助

**--include-attributes** 字符串            （仅限 Windows）包括其属性与属性列表相匹配的文件。 例如： A;些迅驰

**--include-path** 字符串                  复制时仅包括这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如：myFolder;myFolder/subDirName/file.pdf）。

**--include-pattern** 字符串               复制时仅包括这些文件。 此选项支持通配符 (*)。 使用“;”分隔文件。

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为“INFO”）

**--metadata** 字符串                      将这些键值对作为元数据上传到 Azure 存储。

**--no-guess-mime-type**                   阻止 AzCopy 根据文件的扩展名或内容来检测内容类型。

**--overwrite** 字符串                     如果此标志设置为 true，则会覆盖目标上有冲突的文件和 Blob。 可能的值包括“true”、“false”、“ifSourceNewer”和“prompt”。 （默认值为“true”）

**--page-blob-tier** 字符串                使用此 Blob 层将页 Blob 上传到 Azure 存储。 （默认值为“None”）

**--preserve-last-modified-time**          仅当目标为文件系统时才可用。

--默认情况下，**保留 smb 权限**字符串 False。 在感知资源（Windows 和 Azure 文件）之间保留 SMB Acl。 对于下载，你还需要使用`--backup`标志来还原新所有者不是运行 AzCopy 的用户的权限。 此标志适用于文件和文件夹，除非指定了 "仅文件" 筛选器（例如`include-pattern`）。

**--preserve-** 默认情况下为 False。 在 SMB 感知资源（Windows 和 Azure 文件）之间保留 SMB 属性信息（上次写入时间、创建时间、属性位）。 仅传输 Azure 文件支持的属性位;任何其他都将被忽略。 此标志适用于文件和文件夹，除非指定了 "仅文件" 筛选器（例如，包含模式）。 为文件夹传输的信息与用于文件的信息相同，但上次写入时间除外，这种情况下永远不会保留文件夹。

**--保留-所有者**                      仅在下载数据时起作用，并且仅当`--preserve-smb-permissions`使用时才有效。 如果为 true （默认值），则文件所有者和组将保留在下载内容中。 如果此标志设置为 false，将`--preserve-smb-permissions`仍然保留 acl，但所有者和组将基于运行 AzCopy 的用户。

**--put-md5**                             创建每个文件的 MD5 哈希，并将该哈希另存为目标 Blob 或文件的 Content-MD5 属性。 （默认情况下不创建哈希。）仅在上载时可用。

**--recursive**                            从本地文件系统上传时以递归方式检查子目录。

**--s2s-detect-source-changed**           检查源在枚举后是否已更改。

**--s2s-handle-invalid-metadata** 字符串   指定如何处理无效的元数据键。 可用选项： ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。 （默认值为“ExcludeIfInvalid”）

**--s2s-preserve-access-tier**             在服务之间复制过程中保留访问层 请参阅 [Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，确保目标存储帐户支持设置访问层。 如果不支持设置访问层，请使用 s2sPreserveAccessTier=false 来绕过访问层的复制。 （默认值为 true）

**--s2s-preserve-properties**              在服务之间复制过程中保留完整属性。 对于 AWS S3 和 Azure 文件存储的非单一文件源，列出操作不会返回对象和文件的完整属性。 若要保留完整属性，AzCopy 需要对每个对象或文件发送一个附加的请求。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32**      以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--output-type** 字符串   命令输出的格式。 选项包括：text、json。 默认值为“text”。 （默认值为“text”）

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
