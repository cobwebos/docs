---
title: azcopy 作业显示 |Microsoft Docs
description: 本文提供 azcopy job show 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195953"
---
# <a name="azcopy-jobs-show"></a>azcopy 作业显示

显示给定作业 ID 的详细信息。

## <a name="synopsis"></a>概要

如果只提供了作业 ID 而没有标志, 则返回作业的进度摘要。

如果设置`with-status`了标志, 则将显示作业中具有给定值的传输的列表。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 "显示" 命令的帮助内容。|
|--状态字符串|仅列出具有此状态的作业传输的可用值:已启动、成功、失败|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
