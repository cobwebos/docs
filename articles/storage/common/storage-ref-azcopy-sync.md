---
title: azcopy 同步 |Microsoft Docs
description: 本文提供 azcopy 同步命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988237"
---
# <a name="azcopy-sync"></a>azcopy sync

将源位置复制到目标位置。

## <a name="synopsis"></a>概要

上次修改时间用于比较。 如果目标中的上次修改时间较新，则将跳过该文件。

支持的对包括：

- 本地 < > Azure Blob （可以使用 SAS 或 OAuth 身份验证）
- Azure Blob < > Azure Blob （源必须包含 SAS 或可公开访问; 可将 SAS 或 OAuth 身份验证用于目标）
- Azure 文件 < > Azure 文件（源必须包含 SAS 或可公开访问;需要为目标使用 SAS 身份验证

同步命令在以下几个方面与 copy 命令不同：

1. 默认情况下，递归标志为 true，并同步复制所有子目录。 如果递归标志为 false，则同步仅复制目录内的顶级文件。
2. 当在虚拟目录之间进行同步时，如果存在与某个虚拟目录同名的 blob，则向路径添加一个尾随斜杠（请参阅示例）。
3. 如果将 "deleteDestination" 标志设置为 true 或 prompt，则 sync 将删除源中不存在的目标上的文件和 blob。

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

### <a name="advanced"></a>高级

如果未指定文件扩展名，则在从本地磁盘上传时，AzCopy 会根据文件扩展名或内容（如果未指定扩展名）自动检测文件的内容类型。

内置查找表很小，但在 Unix 上，它由本地系统的 mime. 类型文件扩充（如果在以下一个或多个名称下可用）：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 类型是从注册表中提取的。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>示例

同步单个文件：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

与上面的示例相同，但这次还计算文件内容的 MD5 哈希，并将其另存为 blob 的 Content-MD5 属性：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步包含其子目录的整个目录（请注意，默认情况下，recursive 处于启用状态）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

仅同步目录中的顶级文件，但不同步其子目录：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目录中的部分文件（例如：仅 jpg 和 pdf 文件，如果文件名为 "exactName"）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整个目录，但从作用域中排除某些文件（例如：以 foo 开头或以 bar 结尾的每个文件）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

同步单个 blob：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

同步虚拟目录：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

同步与 blob 同名的虚拟目录（向路径添加尾随斜杠以消除歧义）：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

同步 Azure 文件目录（与 Blob 相同的语法）：

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 如果将包含/排除标志一起使用，则只会查看与 include 模式匹配的文件，但与排除模式匹配的文件将始终被忽略。

## <a name="options"></a>选项

**--块大小-mb**浮点在上传到 azure 存储或从 azure 存储下载时，使用此块大小（在 MiB 中指定）。 默认值是根据文件大小自动计算的。 允许使用小数小数（例如：0.25）。

**--check-md5** string 指定在下载时应如何验证严格的 md5 哈希。 此选项仅在下载时可用。 可用值包括： NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 （默认值为 "FailIfDifferent"）。 （默认值为 "FailIfDifferent"）

**--删除-目标**字符串定义是否从目标中删除源中不存在的额外文件。 可以设置为 "true"、"false" 或 "prompt"。 如果设置为 "提示"，则在计划要删除的文件和 blob 之前，会向用户询问一个问题。 （默认值为 "false"）。 （默认值为 "false"）

**--exclude-** attribute string （仅限 Windows）排除其属性与属性列表匹配的文件。 例如： A;些迅驰

**--排除-路径**字符串在复制时排除这些路径。 此选项不支持通配符（*）。 检查相对路径前缀（例如： myFolder; myFolder/subDirName/file .pdf）。 与帐户遍历结合使用时，路径不包含容器名称。

**--排除模式**字符串排除名称与模式列表匹配的文件。 例如： \*.jpg;\*.pdf; exactName

**-h，--帮助**帮助进行同步

**--include-** attribute string （仅限 Windows）仅包括属性与属性列表匹配的文件。 例如： A;些迅驰

**--include-模式**字符串仅包含名称与模式列表匹配的文件。 例如： \*.jpg;\*.pdf; exactName

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求和响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认信息）。 （默认值为 "INFO"）

**--put-md5**                    创建每个文件的 MD5 哈希，并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 （默认情况下不创建哈希。）仅在上载时可用。

**--recursive**                  默认情况下，在目录间同步时，将以递归方式查看子目录。 （默认值为 true）。 （默认值为 true）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|Description|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
