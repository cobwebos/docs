---
title: azcopy 删除 |Microsoft Docs
description: 本文提供 azcopy remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195901"
---
# <a name="azcopy-remove"></a>azcopy 删除

删除 Azure 存储 Blob、文件和 Azure Data Lake Storage Gen2 中的实体。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>示例

使用 SAS 删除单个 blob:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 删除整个虚拟目录:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仅删除虚拟目录 (而不是其子目录) 中的顶层 blob:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

删除虚拟目录 (例如: 仅 jpg 和 pdf 文件, 或 blob 名称为 "exactName") 中的一部分 blob:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

删除整个虚拟目录, 但从作用域中排除某些 blob (例如: 以 foo 开头或以 bar 结尾的每个 blob):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

删除 Data Lake Storage Gen2 中的单个文件 (不支持 include 和 exclude):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

从 Data Lake Storage Gen2 删除单个目录 (不支持 include 和 exclude):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|--exclude string|排除名称与模式列表匹配的文件。 例如: *.jpg;* 。pdf; exactName|
|-h、--help|显示 "删除" 命令的帮助内容。|
|--include 字符串|仅包含名称与模式列表匹配的文件。 例如: *.jpg;* 。pdf; exactName|
|--日志级别字符串|为日志文件定义日志详细级别。 可用级别包括:信息 (所有请求/响应)、警告 (响应缓慢)、错误 (仅失败的请求) 和 NONE (无输出日志)。 (默认值为 "INFO")|
|--recursive|在目录间同步时, 以递归方式查看子目录。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
