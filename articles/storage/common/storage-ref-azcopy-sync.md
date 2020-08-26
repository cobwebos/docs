---
title: azcopy sync | Microsoft Docs
description: 本文提供有关 azcopy sync 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ee2f01e1b7771e71afe49c4b69b1fb39e43f37
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869433"
---
# <a name="azcopy-sync"></a>azcopy sync

将源位置复制到目标位置。

## <a name="synopsis"></a>摘要

上次修改时间用于比较。 如果目标中的上次修改时间与当前时间更近，则会跳过文件。

支持的配对包括：

- 本地 <-> Azure Blob（可以使用 SAS 或 OAuth 身份验证）
- Azure Blob <-> Azure Blob（源必须包含 SAS 或可公开访问；可将 SAS 或 OAuth 身份验证用于目标）
- Azure 文件 <-> Azure 文件（源必须包含 SAS 或可公开访问；应将 SAS 身份验证用于目标）

sync 命令与 copy 命令的不同之处体现在以下几个方面：

1. 默认情况下，递归标志为 true，sync 会复制所有子目录。 如果递归标志为 false，则 sync 只复制目录中的顶级文件。
2. 在虚拟目录之间同步时，如果存在与某个虚拟目录同名的 Blob，则在路径中添加一个尾随斜杠（参考示例）。
3. 如果 `deleteDestination` 将标志设置为 true 或 prompt，则 sync 将删除源中不存在的目标上的文件和 blob。

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

### <a name="advanced"></a>高级

如果未指定文件扩展名，从本地磁盘上传时，AzCopy 会根据文件扩展名或内容自动检测文件的内容类型。

内置的查找表较小，但在 Unix 上，已通过采用以下一个或多个名称的本地系统 mime.types 文件（如果可用）扩充了该查找表：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表提取的。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>示例

同步单个文件：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

与上面相同，但也计算文件内容的 MD5 哈希，然后将该 MD5 哈希保存为 blob 的 Content-MD5 属性。 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步整个目录，包括其子目录 (请注意，默认情况下，递归) ：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

仅同步目录中的文件，但不同步子目录中的文件，而不同步子目录中的文件：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目录中的部分文件 (例如：仅 jpg 和 pdf 文件，如果文件名为) ，则为 `exactName` ：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

同步整个目录，但从作用域中排除某些文件 (例如：以 foo 开头的每个文件或以 bar 结尾) ：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

同步单个 Blob：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

同步虚拟目录：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

同步与 Blob 同名的虚拟目录（在路径中添加尾随斜杠以消除歧义）：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

同步 Azure 文件目录（语法与 Blob 相同）：

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 如果同时使用 include/exclude 标志，只会查找与 include 模式匹配的文件，而始终忽略与 exclude 模式匹配的文件。

## <a name="options"></a>选项

**--块大小-mb** 浮点使用在) 上传到 azure 存储或从 azure 存储下载时，在 MiB 中指定 (块大小。 默认值根据文件大小自动计算。 允许使用小数小数 (例如： `0.25`) 。

**--check-md5** string 指定在下载时应如何验证严格的 md5 哈希。 此选项仅在下载时可用。 可用值包括： `NoCheck` 、 `LogOnly` 、 `FailIfDifferent` 和 `FailIfDifferentOrMissing` 。  (默认 `FailIfDifferent`) 。  (默认值 `FailIfDifferent`) 

**--delete-destination** 字符串   定义是否从目标中删除不在源中的多余文件。 可以设置为 `true` 、 `false` 或 `prompt` 。 如果设置为 `prompt` ，则在计划要删除的文件和 blob 之前，会向用户询问一个问题。  (默认 `false`) 。  (默认值 `false`) 

**--exclude-** attribute string (仅限 Windows) 排除其属性与属性列表匹配的文件。 例如：`A;S;R`

**--排除-路径** 字符串在将源与目标进行比较时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀 (例如： `myFolder;myFolder/subDirName/file.pdf`) 。

**--exclude-pattern** 字符串   排除名称与模式列表相匹配的文件。 例如：`*.jpg;*.pdf;exactName`

**--帮助**    帮助进行同步。

**--include-** attribute string (仅限 Windows) 只包括其属性与属性列表匹配的文件。 例如：`A;S;R`

**--include-pattern** 字符串   仅包括名称与模式列表相匹配的文件。 例如：`*.jpg;*.pdf;exactName`

**--日志级别** 字符串定义日志文件的日志详细级别，可用级别： `INFO` (所有请求和响应) ， `WARNING` (慢速响应) ， `ERROR` (仅) 失败的请求，并且 (`NONE` 没有输出日志) 。  (默认 `INFO`) 。 

**--保留-smb-信息**    默认值为 False。保留 SMB 感知资源（Windows 和 Azure 文件存储）之间的 SMB 属性信息（上次写入时间、创建时间、属性位）。此标志适用于文件和文件夹，除非指定了文件筛选器 (例如，包含-pattern) 。为文件夹传输的信息与用于文件的信息相同，但上次写入时间除外，文件夹未保留该信息。

**--保留-smb 权限**    默认值为 False。保留感知资源（Windows 和 Azure 文件存储）之间的 SMB ACl。此标志适用于文件和文件夹，除非指定了文件筛选器 (例如  `include-pattern`) 。

**--put-md5**     创建每个文件的 MD5 哈希，并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 （默认不会创建哈希。）仅在上传时可用。

**--recursive** `True`默认情况下，在目录间同步时，会以递归方式查看子目录。      (默认 `True`) 。 

**--s2s-保留-访问层**  服务间复制时保留访问层。 请参阅 [Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) ，确保目标存储帐户支持设置访问层。 如果不支持设置访问层，请使用 s2sPreserveAccessTier=false 来绕过访问层的复制。  (默认 `true`) 。 

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   |指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
