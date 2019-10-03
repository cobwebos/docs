---
title: azcopy 副本 |Microsoft Docs
description: 本文提供 azcopy copy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195732"
---
# <a name="azcopy-copy"></a>azcopy 副本

将源数据复制到目标位置。

## <a name="synopsis"></a>概要

支持的方向如下:

- 本地 < > Azure Blob (SAS 或 OAuth 身份验证)
- 本地 < > Azure 文件 (共享/目录 SAS 身份验证)
- local <-> ADLS 第2代 (SAS、OAuth 或 SharedKey authentication)
- Azure Blob (SAS 或公共) < > Azure Blob (SAS 或 OAuth 身份验证)
- Azure 文件 (SAS)-> Azure 块 Blob (SAS 或 OAuth 身份验证)
- AWS S3 (访问密钥)-> Azure 块 Blob (SAS 或 OAuth 身份验证)

有关详细信息, 请参阅示例。

### <a name="advanced"></a>高级

从本地磁盘上传时, AzCopy 会根据文件扩展名或内容 (如果未指定扩展名) 自动检测文件的内容类型。

内置查找表很小, 但在 Unix 上, 它会通过以下一个或多个名称中的文件 (如果可用) 来扩充:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上, MIME 类型是从注册表中提取的。 可以使用标志的帮助关闭此功能。 请参阅标志部分。

> [!IMPORTANT]
> 如果使用命令行设置环境变量, 则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中, 可以使用脚本来提示用户输入其凭据, 并设置环境变量。

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>示例

使用 OAuth 身份验证上传单个文件。

如果尚未登录到 AzCopy, 请在运行以下命令`azcopy login`前使用命令。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

与上面的示例相同, 但这一次还计算文件内容的 MD5 哈希, 并将其另存为 blob 的 Content-MD5 属性:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

使用 SAS 上载单个文件:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用带有管道的 SAS 上载单个文件 (仅限块 blob):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 上传整个目录:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

或

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

使用通配符上载一组带有 SAS 的文件:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

使用通配符上传带有 SAS 的文件和目录:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

使用 OAuth 身份验证下载单个文件。

如果尚未登录到 AzCopy, 请在运行以下命令`azcopy login`前使用命令。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

下载具有 SAS 的单个文件:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

使用带有管道的 SAS 下载单个文件 (仅限块 blob):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

下载包含 SAS 的整个目录:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

使用通配符下载一组带有 SAS 的文件:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

使用通配符下载文件和目录:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

使用 SAS 将单个 blob 复制到另一个 blob:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 OAuth 令牌将单个 blob 和 SAS 复制到另一个 blob。

如果尚未登录到 AzCopy, 请在运行以下命令`azcopy login`前使用命令。 OAuth 令牌用于访问目标存储帐户。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

使用 sas 将完整目录从 blob 虚拟目录复制到另一个 blob 虚拟目录:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

使用 SAS 将帐户数据从 blob 帐户复制到另一个 blob 帐户:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

使用 SAS 将单个对象从 S3 复制到 blob 的访问密钥:

为 S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 将整个目录从 S3 复制到 blob 虚拟目录:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

请 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 参阅, 了解什么 [folder] 对 S3 意味着什么。 为 S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

使用 SAS, 将 S3 服务中的所有存储桶复制到具有 blob 帐户的访问密钥:

为 S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

将 S3 区域中的所有存储桶都复制到具有 SAS 的 blob 帐户的访问密钥:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

为 S3 源设置环境变量 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

## <a name="options"></a>选项

|选项|描述|
|--|--|
|--blob 类型字符串|定义目标中的 blob 类型。 这用于上传 blob 以及在帐户之间复制 (默认值为 "无")。|
|--块 blob 层字符串|使用此 blob 层将块 blob 上传到 Azure 存储。 (默认值为 "无")。|
|--块大小-mb 浮点 |上传到 Azure 存储, 并从 Azure 存储下载时, 请使用此块大小 (在 MiB 中指定)。 默认值是根据文件大小自动计算的。 允许使用小数小数 (例如:0.25)。|
|--cache 控制字符串|设置缓存控制标头。 下载时返回。|
|--check-md5 string|指定在下载时应如何验证严格的 MD5 哈希。 仅在下载时可用。 可用选项：NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 (默认值为 "FailIfDifferent")|
|--内容处理字符串|设置内容处置标头。 下载时返回。|
|--content 编码字符串|设置 "内容编码" 标头。 下载时返回。|
|--content-language 字符串|设置 content-type 标头。 下载时返回。|
|--content-type 字符串 |指定文件的内容类型。 表示无推测 mime 类型。 下载时返回。|
|--exclude string|复制时排除这些文件。 支持使用 *。|
|--不包含 blob-类型字符串|根据需要, 还可以指定从容器或帐户复制 blob 时要排除的 blob 的类型 (BlockBlob/PageBlob/AppendBlob)。 使用此标志不适用于将数据从非 azure 服务复制到服务。 多个 blob 应由 ";" 分隔。|
|--符号链接|从本地文件系统上传时, 请遵循符号链接。|
|--from 字符串|(可选) 指定源目标组合。 例如：LocalBlob, BlobLocal, LocalBlobFS.|
|-h、--help|显示复制命令的帮助内容。 |
|--日志级别字符串|为日志文件定义日志详细级别, 可用级别:信息 (所有请求/响应)、警告 (响应缓慢)、错误 (仅失败的请求) 和 NONE (无输出日志)。 (默认值为 "信息")。|
|--元数据字符串|将这些键/值对作为元数据上传到 Azure 存储。|
|--非推测-mime 类型|阻止 AzCopy 根据文件的扩展名或内容来检测内容类型。|
|--overwrite|如果此标志设置为 true, 则覆盖目标上的冲突文件/blob。 (默认值为 true)。|
|--页 blob 层字符串 |使用此 blob 层将页 blob 上传到 Azure 存储。 (默认值为 "无")。|
|--保留-上次修改时间|仅当 destination 为文件系统时可用。|
|--put-md5|创建每个文件的 MD5 哈希, 并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 (默认情况下不创建哈希。)仅在上载时可用。|
|--recursive|从本地文件系统上传时, 按递归方式查看子目录。|
|--s2s-已更改|检查源在枚举后是否已更改。 对于 S2S 副本, 作为 "源" 为远程资源, 验证源是否已更改需要额外的请求成本。|
|--s2s-句柄-元数据字符串无效 |指定处理无效的元数据密钥的方式。 可用选项：ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (默认值为 "ExcludeIfInvalid")。|
|--s2s-保留-访问层|服务间复制时保留访问层。 请参阅[Azure Blob 存储: "热"、"冷" 和 "存档" 访问层](../blobs/storage-blob-storage-tiers.md), 确保目标存储帐户支持设置访问层。 如果不支持设置访问层, 请使用 s2sPreserveAccessTier = false 来绕过复制访问层。  (默认值为 true)。|
|--s2s-保留-属性|在服务间复制服务时保留完整的属性。 对于 S3 和 Azure 文件非单一文件源, 因为列表操作不返回对象和文件的完整属性, 要保留完整属性, AzCopy 需要为每个对象和文件发送一个额外的请求。 (默认值为 true)。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
