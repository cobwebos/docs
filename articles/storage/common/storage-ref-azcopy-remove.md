---
title: azcopy 删除 |Microsoft Docs
description: 本文提供 azcopy remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033997"
---
# <a name="azcopy-remove"></a>azcopy remove

从 Azure 存储帐户中删除 blob 或文件。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

使用 SAS 删除单个 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 删除整个虚拟目录：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仅删除虚拟目录（而不是其子目录）中的顶层 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

删除虚拟目录（例如：仅 jpg 和 pdf 文件，或 blob 名称为 "exactName"）中的一部分 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

删除整个虚拟目录，但从作用域中排除某些 blob （例如：以 foo 开头或以 bar 结尾的每个 blob）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

通过在文件中放置特定 blob 和虚拟目录，将其相对路径（非 URL 编码）删除：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

从具有分层命名空间（包含/排除不支持）的 Blob 存储帐户中删除单个文件。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

删除单一目录具有分层命名空间（包含/排除不支持）的 Blob 存储帐户：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>选项

**--排除路径字符串**     删除时排除这些路径。 此选项不支持通配符（*）。 检查相对路径前缀。 例如： myFolder; myFolder/subDirName/file .pdf。

**--排除模式**字符串排除名称与模式列表匹配的文件。 例如： *.jpg;* 。pdf; exactName

**-h，--** 用于删除的帮助帮助

**--include-path**字符串在删除时仅包含这些路径。 此选项不支持通配符（*）。 检查相对路径前缀。 例如： myFolder; myFolder/subDirName/file .pdf

**--include-模式**字符串仅包含名称与模式列表匹配的文件。 例如： *.jpg;* 。pdf; exactName

**--文件列表**字符串定义文件的位置，该文件包含要删除的文件和目录的列表。 相对路径应由换行符分隔，路径不应进行 URL 编码。

**--日志级别**字符串定义日志文件的日志详细级别。 可用级别包括： INFO （所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为 "INFO"）（默认值为 "INFO"）

**--recursive**               在目录间同步时，以递归方式查看子目录。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
