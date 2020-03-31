---
title: azcopy make | Microsoft Docs
description: 本文提供有关 azcopy make 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 67d685684c5227377a0f8a7e822a06e785a69d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034047"
---
# <a name="azcopy-make"></a>azcopy make

创建容器或文件共享。

## <a name="synopsis"></a>概要

创建由给定资源 URL 表示的容器或文件共享。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示 make 命令的帮助内容。 |
|--quota-gb uint32|指定共享的最大大小（以 GB (GiB) 为单位），0 表示接受文件服务的默认配额。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|

## <a name="see-also"></a>请参阅

- [阿兹比贝](storage-ref-azcopy.md)
