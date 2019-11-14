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
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033749"
---
# <a name="azcopy-env"></a>azcopy env

显示可配置 AzCopy 的行为的环境变量。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中，可以使用脚本来提示用户输入其凭据，并设置环境变量。

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="options"></a>选项

|选项|说明|
|--|--|
|-h、--help|显示 env 命令的帮助内容。 |
|--区分大小写|显示敏感/机密环境变量。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
