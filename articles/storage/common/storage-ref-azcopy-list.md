---
title: azcopy 列表 |Microsoft Docs
description: 本文提供 azcopy list 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195940"
---
# <a name="azcopy-list"></a>azcopy 列表

列出给定资源中的实体。

## <a name="synopsis"></a>概要

当前版本仅支持 Blob 容器。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>示例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 list 命令的帮助内容。|
|--计算机可读|列出文件大小 (以字节为单位)。|
|--万像素|按1000的顺序显示单位, 而不是1024。|
|--正在运行-计数|计算文件总数及其大小。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
