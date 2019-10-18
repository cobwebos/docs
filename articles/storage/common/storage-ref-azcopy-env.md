---
title: azcopy 环境 |Microsoft Docs
description: 本文提供 azcopy env 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7ceff4df320aa1fbc3aa6e601c61f6407fd762e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514753"
---
# <a name="azcopy-env"></a>azcopy env

显示可配置 AzCopy 的行为的环境变量。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中，可以使用脚本来提示用户输入其凭据，并设置环境变量。

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 env 命令的帮助内容。 |
|--区分大小写|显示敏感/机密环境变量。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
