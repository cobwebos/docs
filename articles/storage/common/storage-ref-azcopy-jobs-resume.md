---
title: azcopy 作业恢复 |Microsoft Docs
description: 本文提供 azcopy job resume 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195784"
---
# <a name="azcopy-jobs-resume"></a>azcopy 作业恢复

恢复具有给定作业 ID 的现有作业。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|--destination-sas 字符串|给定 JobId 的目标 SAS。|
|--exclude string|筛选器恢复作业时排除这些失败的传输。 文件应由 ";" 分隔。|
|-h、--help|显示 resume 命令的帮助内容。|
|--include 字符串|筛选器: 恢复作业时仅包含这些失败的传输。 文件应由 ";" 分隔。|
|--source-sas 字符串 |给定 JobId 的源 SAS。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
