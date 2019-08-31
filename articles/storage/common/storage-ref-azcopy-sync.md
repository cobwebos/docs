---
title: azcopy 同步 |Microsoft Docs
description: 本文提供 azcopy 同步命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195758"
---
# <a name="azcopy-sync"></a>azcopy 同步

将源位置复制到目标位置。

## <a name="synopsis"></a>概要

上次修改时间用于比较。 如果目标中的上次修改时间较新, 则将跳过该文件。

支持的对包括:

- 本地 < > Azure Blob (可以使用 SAS 或 OAuth 身份验证)

同步命令在以下几个方面与 copy 命令不同:

  1. 默认情况下, 递归标志为 on。
  2. 源和目标不应包含模式 (如 * 或？)。
  3. Include 和 exclude 标志可以是与文件名匹配的模式列表。 有关说明, 请参阅 "示例" 部分。
  4. 如果目标中存在源中不存在的文件或 blob, 则系统将提示用户删除它们。

     可以通过使用相应的标志自动回答删除问题来静默此提示。

### <a name="advanced"></a>高级

从本地磁盘上传时, AzCopy 会根据文件扩展名或内容 (如果未指定扩展名) 自动检测文件的内容类型。

内置查找表很小, 但在 Unix 上, 它由本地系统的 mime. 类型文件扩充 (如果在以下一个或多个名称下可用):

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上, MIME 类型是从注册表中提取的。

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>示例

同步单个文件:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

与上面的示例相同, 但这次还计算文件内容的 MD5 哈希, 并将其另存为 blob 的 Content-MD5 属性:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步包含其子目录的整个目录 (请注意, 默认情况下, recursive 处于启用状态):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

仅同步目录中的顶级文件, 但不同步其子目录:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目录中的部分文件 (例如: 仅 jpg 和 pdf 文件, 如果文件名为 "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整个目录, 但从作用域中排除某些文件 (例如: 以 foo 开头或以 bar 结尾的每个文件):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> 如果将包含/排除标志一起使用, 则只会查看与 include 模式匹配的文件, 但与排除模式匹配的文件将始终被忽略。

## <a name="options"></a>选项

|选项|描述|
|--|--|
|--块大小-mb 浮点|上传到 Azure 存储或从 Azure 存储下载时, 请使用此块大小 (在 MiB 中指定)。 默认值是根据文件大小自动计算的。 允许使用小数小数 (例如:0.25)。|
|--check-md5 string|指定在下载时应如何验证严格的 MD5 哈希。 此选项仅在下载时可用。 可用值包括:NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 (默认值为 "FailIfDifferent")。|
|--删除-目标字符串|定义是否从目标中删除源中不存在的额外文件。 可以设置为 "true"、"false" 或 "prompt"。 如果设置为 "提示", 则在计划要删除的文件和 blob 之前, 会向用户询问一个问题。 (默认值为 "false")。|
|--exclude string|排除名称与模式列表匹配的文件。 例如: *.jpg;* 。pdf; exactName。|
|-h、--help|显示同步命令的帮助内容。|
|--include 字符串|仅包含名称与模式列表匹配的文件。 例如: *.jpg;* 。pdf; exactName。|
|--日志级别字符串|为日志文件定义日志详细级别, 可用级别:信息 (所有请求/响应)、警告 (响应缓慢)、错误 (仅失败的请求) 和 NONE (无输出日志)。 (默认值为 "信息")。|
|--put-md5|创建每个文件的 MD5 哈希, 并将哈希保存为目标 blob 或文件的 Content-MD5 属性。 (默认情况下不创建哈希。)仅在上载时可用。|
|--recursive|默认情况下, 在目录间同步时, 将以递归方式查看子目录。 (默认值为 true)。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
