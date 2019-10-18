---
title: azcopy make |Microsoft Docs
description: 本文提供了 azcopy make 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514688"
---
# <a name="azcopy-make"></a>azcopy make

创建容器或文件共享。

## <a name="synopsis"></a>概要

创建给定资源 URL 表示的容器或文件共享。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>示例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 make 命令的帮助内容。 |
|--quota-gb uint32|指定以千兆字节（GiB）表示的最大共享大小，0表示接受文件服务的默认配额。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
