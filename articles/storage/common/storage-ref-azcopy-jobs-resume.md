---
title: azcopy 作业恢复 |Microsoft Docs
description: 本文提供 azcopy job resume 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034147"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

恢复具有给定作业 ID 的现有作业。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="options"></a>选项

|选项|说明|
|--|--|
|--destination-sas 字符串|给定 JobId 的目标 SAS。|
|--exclude string|筛选器：恢复作业时排除失败的传输。 文件应由 ";" 分隔。|
|-h、--help|显示 resume 命令的帮助内容。|
|--include 字符串|筛选器：恢复作业时仅包含这些失败的传输。 文件应由 ";" 分隔。|
|--source-sas 字符串 |给定 JobId 的源 SAS。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
