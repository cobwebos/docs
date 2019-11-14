---
title: azcopy 列表 |Microsoft Docs
description: 本文提供 azcopy list 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034093"
---
# <a name="azcopy-list"></a>azcopy list

列出给定资源中的实体。

## <a name="synopsis"></a>概要

当前版本仅支持 Blob 容器。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>选项

|选项|说明|
|--|--|
|-h、--help|显示 list 命令的帮助内容。|
|--计算机可读|列出文件大小（以字节为单位）。|
|--万像素|按1000的顺序显示单位，而不是1024。|
|--正在运行-计数|计算文件总数及其大小。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
