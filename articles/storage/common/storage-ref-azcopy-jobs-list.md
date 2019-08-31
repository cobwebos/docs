---
title: azcopy 作业列表 |Microsoft Docs
description: 本文提供 azcopy job list 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ff10a1e4544f80d618d6989ab251096e6cb77baf
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195979"
---
# <a name="azcopy-jobs-list"></a>azcopy 作业列表

显示有关所有作业的信息。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs list [flags]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 list 命令的帮助内容。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
