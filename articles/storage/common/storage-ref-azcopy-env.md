---
title: azcopy 环境 |Microsoft Docs
description: 本文提供 azcopy env 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 15e72493190e1bc56e779c22695bc51bd05da940
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195771"
---
# <a name="azcopy-env"></a>azcopy 环境

显示可配置 AzCopy 的行为的环境变量。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果使用命令行设置环境变量, 则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中, 可以使用脚本来提示用户输入其凭据, 并设置环境变量。

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 env 命令的帮助内容。 |
|--区分大小写|显示敏感/机密环境变量。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [azcopy](storage-ref-azcopy.md)
