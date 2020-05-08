---
title: azcopy remove | Microsoft Docs
description: 本文提供有关 azcopy remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f76489f384f233f65eb8fcca3a8359cd5b67c20a
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780719"
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

删除包含 SAS 的单个 Blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

删除包含 SAS 的整个虚拟目录：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仅删除虚拟目录中的顶级 blob，而不删除其子目录中的：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

删除虚拟目录中的一部分 blob（例如：仅删除 jpg 和 pdf 文件，或者删除 blob 名为“exactName”的 blob）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

删除整个虚拟目录，但从范围中排除某些 blob（例如：以 foo 开头或以 bar 结尾的每个 blob）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

删除特定 blob 和虚拟目录，方法是将其相对路径（非 URL 编码）置于文件中：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

在采用分层命名空间的 Blob 存储帐户中删除单个文件（不支持 include/exclude）。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

在采用分层命名空间的 Blob 存储帐户中删除单个目录（不支持 include/exclude）：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>选项

**--删除-快照**字符串默认情况下，如果 blob 具有快照，则删除操作将失败。 指定 "include" 以删除根 blob 及其所有快照;或者指定 "仅" 以删除快照，但保留根 blob。

**--排除-路径**字符串删除时排除这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀。 例如：myFolder;myFolder/subDirName/file.pdf。

**--exclude-pattern** 字符串   排除名称与模式列表相匹配的文件。 例如：*.jpg;*.pdf;exactName

**--force-只读**   删除 Azure 文件文件或文件夹时，即使现有对象已设置为只读属性，也强制执行删除操作

**-h，--** 用于删除的帮助帮助

**--include-path** 字符串      删除时仅包括这些路径。 此选项不支持通配符 (*)。 检查相对路径前缀。 例如：myFolder;myFolder/subDirName/file.pdf

**--include-pattern** 字符串   仅包括名称与模式列表相匹配的文件。 例如：*.jpg;*.pdf;exactName

**--list-of-files** string     定义要删除的文件和目录列表所在文件的位置。 相对路径应由换行符分隔，路径不应进行 URL 编码。

**--log-level** 字符串         定义日志文件的日志详细程度。 可用级别包括： INFO （所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为“INFO”）（默认值为“INFO”）

**--recursive**                在目录之间同步时，以递归方式查看子目录。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--可信-microsoft 后缀字符串   | 指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为 "*core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
