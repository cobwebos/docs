---
title: azcopy 注销 |Microsoft Docs
description: 本文提供 azcopy 注销命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513501"
---
# <a name="azcopy-logout"></a>azcopy logout

记录用户并终止对 Azure 存储资源的访问。

## <a name="synopsis"></a>概要

此命令将删除当前用户的所有缓存的登录信息。

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>选项

|选项|描述|
|--|--|
|-h、--help|显示注销命令的帮助内容。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
