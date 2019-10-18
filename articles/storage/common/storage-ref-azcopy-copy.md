---
title: azcopy 副本 |Microsoft Docs
description: 本文提供 azcopy copy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7f08c175972826a8b226d7e80f563ac71ba23db
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514760"
---
# <a name="azcopy-copy"></a>azcopy copy

将源数据复制到目标位置。

## <a name="synopsis"></a>概要

将源数据复制到目标位置。 支持的方向如下：

  - 本地 < > Azure Blob （SAS 或 OAuth 身份验证）
  - 本地 < > Azure 文件（共享/目录 SAS 身份验证）
  - local <-> ADLS 第2代（SAS、OAuth 或 SharedKey authentication）
  - Azure Blob （SAS 或公用）-> Azure Blob （SAS 或 OAuth 身份验证）
  - Azure Blob （SAS 或公用）-> Azure 文件（SAS）
  - Azure 文件（SAS）-> Azure 文件（SAS）
  - Azure 文件（SAS）-> Azure Blob （SAS 或 OAuth 身份验证）
  - AWS S3 （访问密钥）-> Azure 块 Blob （SAS 或 OAuth 身份验证）

有关详细信息，请参阅示例。

## <a name="advanced"></a>高级

从本地磁盘上传时，AzCopy 会根据文件扩展名或内容（如果未指定扩展名）自动检测文件的内容类型。

内置查找表很小，但在 Unix 上，它会通过以下一个或多个名称中的文件（如果可用）来扩充：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表中提取的。 可以使用标志的帮助关闭此功能。 请参阅标志部分。

如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中，可以使用脚本来提示用户输入其凭据，并设置环境变量。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>示例

使用 OAuth 身份验证上传单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 AzCopy login 命令。

- azcopy cp "/path/to/file.txt" "https：//[account]/[容器]/[path/to/blob]"

与上面的示例相同，但这一次还计算文件内容的 MD5 哈希，并将其另存为 blob 的 Content-MD5 属性：

- azcopy cp "/path/to/file.txt" "https：//[account]/[容器]/[path/to/blob]"--put-md5

使用 SAS 令牌上传单个文件：

- azcopy cp "/path/to/file.txt" "https：//[account]/[container]/[path/to/blob]？[SAS] "

使用 SAS 令牌和管道（仅限块 blob）上载单个文件：
  
- cat "/path/to/file.txt" |azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] "

使用 SAS 令牌上传整个目录：
  
- azcopy cp "/path/to/dir" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true

或

- azcopy cp "/path/to/dir" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true--put-md5

使用 SAS 令牌和通配符（*）字符上传一组文件：

- azcopy cp "/path/*foo/* bar/* .pdf" "https：//[account]/[container]/[path/to/directory]？[SAS] "

使用 SAS 令牌和通配符（*）字符上传文件和目录：

- azcopy cp "/path/*foo/* bar *" "https：//[account]/[container]/[path/to/directory]？[SAS] "--recursive = true

使用 OAuth 身份验证下载单个文件。 如果尚未登录到 AzCopy，请在运行以下命令之前运行 AzCopy login 命令。

- azcopy cp "https：//[account]/path/to/file.txt/[容器]/[path/to/blob]" ""

使用 SAS 令牌下载单个文件：

- azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] ""/path/to/file.txt "

使用 SAS 令牌下载单个文件，然后通过管道将输出传递到文件（仅限块 blob）：
  
- azcopy cp "https：//[account]. .net/[容器]/[路径/到/blob]？[SAS] ">"/path/to/file.txt "

使用 SAS 令牌下载整个目录：
  
- azcopy cp "https：//[account]. .net/[容器]/[路径/to/directory]？[SAS] ""/path/to/dir "--recursive = true

有关在 Url 中使用通配符（*）的说明：

仅有两种支持的方法可以在 URL 中使用通配符。 

- 可以在 URL 的最后一个正斜杠（/）后使用一个。 这会将目录中的所有文件直接复制到目标，而不将其放入子目录。

- 你还可以使用容器名称中的一个，前提是 URL 只引用容器而不是 blob。 您可以使用此方法从一小部分容器获取文件。

下载目录的内容，而不复制包含目录本身。

- azcopy cp "https：//[srcaccount]. .net/[容器]/[path/to/folder]/*？[SAS] ""/path/to/dir "

下载整个存储帐户。

- azcopy cp "https：//[srcaccount]/" "/path/to/dir"--recursive

使用容器名称中的通配符（*），下载存储帐户中的容器的子集。

- azcopy cp "https：//[srcaccount]/[容器 * 名称]" "/path/to/dir"--递归

使用 SAS 令牌将单个 blob 复制到另一个 blob。

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/到/blob]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/到/blob]？[SAS] "

使用 SAS 令牌和 OAuth 令牌将单个 blob 复制到另一个 blob。 必须在源帐户 URL 末尾使用 SAS 令牌，但如果使用 AzCopy login 命令登录到 AzCopy，则目标帐户不需要。 

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/到/blob]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/到/blob]"

使用 SAS 令牌将一个 blob 虚拟目录复制到另一个 blob：

- azcopy cp "https：//[srcaccount]. .net/[容器]/[路径/to/directory]？[SAS] "https：//[destaccount]. .net/[容器]/[路径/to/directory]？[SAS] "--recursive = true

使用 SAS 令牌将所有 blob 容器、目录和 blob 从存储帐户复制到另一个容器：

- azcopy cp "https：//[srcaccount][SAS] "" https：//[destaccount][SAS] "--recursive = true

使用访问密钥和 SAS 令牌将单个对象从 Amazon Web Services （AWS） S3 复制到 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。
  
- azcopy cp "https://s3.amazonaws.com/ [bucket]/[object]" "https：//[destaccount]/[container]/[path/to/blob]？[SAS] "

使用访问密钥和 SAS 令牌将整个目录从 AWS S3 复制到 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ [bucket]/[folder]" "https：//[destaccount]/[container]/[path/to/directory]？[SAS] "--recursive = true

请参阅 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html ，以更好地了解 "[文件夹]" 占位符。

使用访问密钥和 SAS 令牌将所有存储桶从 Amazon Web Services （AWS）复制到 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ " https：//[destaccount]. l w？[SAS] "--recursive = true

使用访问密钥和 SAS 令牌将所有存储桶从 Amazon Web Services （AWS）区域复制到 Blob 存储。 首先，为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3- [region]. amazonaws/" "https：//[destaccount][SAS] "--recursive = true

使用存储桶名称中的通配符（*）复制存储桶的子集。 如前面的示例所示，你将需要一个访问密钥和一个 SAS 令牌。 请确保为 AWS S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ [bucket * name]/" "https：//[destaccount][SAS] "--recursive = true

## <a name="options"></a>选项

**--blob 类型**字符串定义目标中的 blob 类型。 这用于上传 blob 以及在帐户之间复制（默认值为 "检测"）。 有效值包括 "检测"、"BlockBlob"、"PageBlob" 和 "AppendBlob"。 在帐户之间复制时，值 "检测" 会导致 AzCopy 使用源 blob 的类型来确定目标 blob 的类型。 在上传文件时，"检测" 确定该文件是基于文件扩展名的 VHD 还是 VHDX 文件。 如果文件网 VHD 或 VHDX 文件，则 AzCopy 会将文件视为页 blob。 （默认值为 "检测"）

**--块 blob 层**字符串上传将块 blob 用于使用此 blob 层的 Azure 存储。 （默认值为 "无"）

**--块大小-mb**浮点：在上传到 azure 存储时使用此块大小（在 MiB 中指定），并从 Azure 存储空间下载。 默认值是根据文件大小自动计算的。 允许使用小数小数（例如：0.25）。

**--cache 控制**字符串设置缓存控制标头。 下载时返回。

**--检查长度**                        传输后检查目标上的文件的长度。 如果源和目标之间不匹配，则将传输标记为失败。 （默认值为 true）

**--check-md5** string 指定在下载时应如何验证严格的 md5 哈希。 仅在下载时可用。 可用选项： NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 （默认值为 "FailIfDifferent"）（默认值为 "FailIfDifferent"）

**--内容处置**字符串设置内容处置标头。 下载时返回。

**--content 编码**字符串设置内容编码标头。 下载时返回。

**--content-language** string 设置 content-type 标头。 下载时返回。

**--content-type** string 指定文件的内容类型。 表示无推测 mime 类型。 下载时返回。

**--解压缩**                          下载文件时自动解压缩文件（如果其内容编码表明它们已压缩）。 支持的内容编码值为 "gzip" 和 "deflate"。 ". Gz"/"gzip" 或 "zz" 的文件扩展名不是必需的，但如果存在，则将其删除。

**--exclude-** attribute string （仅限 Windows）排除其属性与属性列表匹配的文件。 例如： A;些迅驰

**--exclude-blob-类型**字符串（可选）在从容器或帐户复制 blob 时，指定要排除的 blob 的类型（BlockBlob/PageBlob/AppendBlob）。 使用此标志不适用于将数据从非 azure 服务复制到服务。 多个 blob 应由 ";" 分隔。

**--排除-路径**字符串在复制时排除这些路径。 此选项不支持通配符（*）。 检查相对路径前缀（例如： myFolder; myFolder/subDirName/file .pdf）。 与帐户遍历结合使用时，路径不包含容器名称。

**--exclude**在复制时排除这些文件。 此选项支持通配符（*）

**--符号链接**                     从本地文件系统上传时，请遵循符号链接。

**--从-到**字符串（可选）指定源目标组合。 例如： LocalBlob、BlobLocal、LocalBlobFS。

**-h，--** 复制的帮助帮助

**--include-** attribute string （仅限 Windows）包括其属性与属性列表匹配的文件。 例如： A;些迅驰

**--include-path**字符串在复制时仅包含这些路径。 此选项不支持通配符（*）。 检查相对路径前缀（例如： myFolder; myFolder/subDirName/file .pdf）。

**--include-模式**字符串在复制时仅包含这些文件。 此选项支持通配符（*）。 使用 ";" 分隔文件。

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为 "INFO"）。 （默认值为 "INFO"）

**--** 将这些键值对作为元数据上传到 Azure 存储的元数据字符串。

**--非推测-mime 类型**                  阻止 AzCopy 根据文件的扩展名或内容来检测内容类型。

**--** 如果此标志设置为 true，则覆盖字符串将覆盖目标上的冲突文件和 blob。 （默认值为 "true"）可能的值包括 "true"、"false" 和 "prompt"。 （默认值为 "true"）

**--页 blob 层**字符串使用此 blob 层将页 Blob 上传到 Azure 存储。 （默认值为 "无"）。 （默认值为 "无"）

**--保留-上次修改时间**         仅当 destination 为文件系统时可用。

**--put-md5**                            创建每个文件的 MD5 哈希，并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 （默认情况下不创建哈希。）仅在上载时可用。

**--recursive**                           从本地文件系统上传时，按递归方式查看子目录。

**--s2s-已更改**          检查源在枚举后是否已更改。

**--s2s-handle-metadata** string 指定如何处理无效的元数据键。 可用选项： ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。 （默认值为 "ExcludeIfInvalid"）。 （默认值为 "ExcludeIfInvalid"）

**--s2s-保留-访问层**            服务间复制时保留访问层。 请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，确保目标存储帐户支持设置访问层。 如果不支持设置访问层，请使用 s2sPreserveAccessTier = false 来绕过复制访问层。 （默认值为 true）。  （默认值为 true）

**--s2s-保留-属性**             在服务间复制服务时保留完整的属性。 对于 AWS S3 和 Azure 文件的非单一文件源，列表操作不返回对象和文件的完整属性。 若要保留完整属性，AzCopy 需要为每个对象或文件发送一个附加请求。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32**     以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。

**--输出类型**字符串格式的命令输出。 选项包括：文本、json。 默认值为 "text"。 （默认值为 "text"）

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
