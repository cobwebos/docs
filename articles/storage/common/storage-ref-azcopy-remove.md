---
title: azcopy remove | Microsoft Docs
description: 本文提供有关 azcopy remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d79b647d216fe28241e5891def574ab598304828
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078214"
---
# <a name="azcopy-remove"></a>azcopy remove

从 Azure 存储帐户中删除 blob 或文件。

## <a name="synopsis"></a>摘要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

使用 SAS 令牌删除单个 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 令牌删除整个虚拟目录：
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仅删除虚拟目录内的 blob，但不要删除这些子目录中的任何子目录或 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

删除虚拟目录中的一部分 blob (例如：仅删除 jpg 和 pdf 文件; 如果 blob 名称为) ，则为： `exactName`

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

删除整个虚拟目录，但从作用域中排除某些 blob (例如：以 foo 开头或以 bar 结尾的每个 blob) ：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

删除特定 blob 和虚拟目录，方法是将其相对路径（非 URL 编码）置于文件中：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
从具有分层命名空间 (包含/排除不受支持) 的 Blob 存储帐户中删除单个文件：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

从具有分层命名空间 (包含/排除不受支持) 的 Blob 存储帐户中删除单个目录：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>选项

**--delete-snapshots** 字符串：如果 blob 有快照，删除操作默认将失败。 指定 `include` 以删除根 blob 及其所有快照; 也可以指定 `only` 仅删除快照但保留根 blob。

**--排除-路径** 字符串删除时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀。 例如：`myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern** 字符串   排除名称与模式列表相匹配的文件。 例如： `*.jpg` ; `*.pdf` ;`exactName`

**--force-只读**   删除 Azure 文件文件或文件夹时，即使现有对象已设置为只读属性，也强制执行删除操作。

**--帮助**   帮助删除。

**--include-path** 字符串在删除时仅包含这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀。 例如：`myFolder;myFolder/subDirName/file.pdf`

**--include-模式** 字符串仅包含名称与模式列表匹配的文件。 例如： * `.jpg` ;* `.pdf` ;`exactName`

**--文件列表** 字符串定义文件的位置，其中包含要删除的文件和目录的列表。 相对路径应由换行符分隔，路径不应进行 URL 编码。 

**--版本列表** 字符串指定一个文件，其中每个版本 id 都在单独的行上列出。 确保源必须指向单个 blob，并且使用此标志在文件中指定的所有版本 id 必须仅属于源 blob。 给定 blob 的指定版本 id 将从 Azure 存储中删除。 

**--日志级别** 字符串定义日志文件的日志详细级别。 可用级别包括： `INFO` (所有请求/响应) ， `WARNING` (慢速响应) ， `ERROR` () 失败的请求，并且 (`NONE` 没有输出日志) 。  (默认 `INFO`)  (默认值 `INFO`) 

**--recursive**    在目录间同步时以递归方式查看子目录。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   |指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
