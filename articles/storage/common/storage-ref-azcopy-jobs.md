---
title: azcopy 作业 |Microsoft Docs
description: 本文提供 azcopy 作业命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514730"
---
# <a name="azcopy-jobs"></a>azcopy jobs

与管理作业相关的子命令。

## <a name="examples"></a>示例

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示作业命令的帮助内容。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
- [azcopy 作业列表](storage-ref-azcopy-jobs-list.md)
- [azcopy 作业恢复](storage-ref-azcopy-jobs-resume.md)
- [azcopy 作业显示](storage-ref-azcopy-jobs-show.md)
