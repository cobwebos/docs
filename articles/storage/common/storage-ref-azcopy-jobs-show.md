---
title: azcopy 作业显示 |Microsoft Docs
description: 本文提供 azcopy job show 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513449"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

显示给定作业 ID 的详细信息。

## <a name="synopsis"></a>概要

如果只提供了作业 ID 而没有标志，则返回作业的进度摘要。

运行此命令时显示的字节计数和完成百分比只反映在该作业中完成的文件。 它们不反映部分完成的文件。

如果设置了 `with-status` 标志，则将显示作业中具有给定值的传输的列表。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 "显示" 命令的帮助内容。|
|--状态字符串|仅列出具有此状态的作业传输，可用值：已启动、成功、失败|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
