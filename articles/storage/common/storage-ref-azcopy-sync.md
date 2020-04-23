---
title: azcopy sync | Microsoft Docs
description: 本文提供有关 azcopy sync 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086022"
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
3. 如果将“deleteDestination”标志设置为 true 或 prompt，则 sync 将删除目标中存在的，但在源中不存在的文件和 Blob。

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

> [!NOTE]
> 目标 blob*必须*存在。 用于`azcopy copy`复制目标中尚不存在的单个文件。 否则，将发生以下错误： `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`。

与前面的示例相同，但这一次此命令还会计算文件内容的 MD5 哈希，并将其另存为 Blob 的 Content-MD5 属性：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步整个目录，包括其子目录（请注意，默认已启用递归）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

仅同步目录中的顶级文件，而不同步其子目录：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目录中的一部分文件（例如：仅同步 jpg 和 pdf 文件，或者同步文件名为“exactName”的文件）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整个目录，但从同步范围中排除某些文件（例如：以 foo 开头或以 bar 结尾的每个文件）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
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

**--block-size-mb** 浮点数         在上传到 Azure 存储或从 Azure 存储下载时使用此块大小（以 MiB 为单位）。 默认值根据文件大小自动计算。 允许小数分数（例如：0.25）。

**--检查 md5**字符串 指定下载时应严格验证 MD5 哈希。 此选项仅在下载时可用。 可用值包括：无检查、仅日志、失败（如果已转移）、失败转移或缺失。 （默认值为“FailIfDifferent”）。 （默认值为“FailIfDifferent”）

**--delete-destination** 字符串   定义是否从目标中删除不在源中的多余文件。 可设置为 true、false 或 prompt。 如果设置为 prompt，则在计划要删除的文件和 Blob 之前，系统会向用户提问。 （默认值为“false”）。 （默认值为“false”）

**--排除属性**字符串（仅限 Windows） 排除其属性与属性列表匹配的文件。 例如：A;S;R

**--外**字符串复制时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀（例如：myFolder;myFolder/subDirName/file.pdf）。 与帐户遍历结合使用时，路径不包含容器名称。

**--排除模式**字符串 排除名称与模式列表匹配的文件。 例如：\*.jpg;\*.pdf;exactName

**-h、--help**                         sync 命令的帮助

**--include-attributes** 字符串   （仅限 Windows）仅包括其属性与属性列表相匹配的文件。 例如：A;S;R

**--包括模式**字符串 仅包括名称与模式列表匹配的文件。 例如：\*.jpg;\*.pdf;exactName

**--日志级**字符串 定义日志文件的日志详细性、可用级别：INFO（所有请求和响应）、警告（慢速响应）、ERROR（仅失败的请求）和 NONE（无输出日志）。 （默认值为 INFO）。 （默认值为“INFO”）

**--put-md5**                    创建每个文件的 MD5 哈希，并将哈希保存为目标 blob 或文件的内容-MD5 属性。 （默认情况下不会创建哈希。仅在上载时可用。

**--recursive**                   默认值为 True，即，在目录之间同步时，将以递归方式查看子目录。 （默认值为 true）。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
