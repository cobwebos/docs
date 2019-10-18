---
title: 清理作业 azcopy |Microsoft Docs
description: 本文提供 azcopy job clean 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518507"
---
# <a name="azcopy-jobs-clean"></a>azcopy 作业清理

删除所有作业的所有日志和计划文件

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>示例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>选项

**-h、--help**               清理帮助。

**--with-status** string 仅删除具有此状态的作业，可用值： "已取消"、"已完成"、"已失败、正在进行"、"所有" （默认为 "全部"）

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32**     以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。

**--输出类型**字符串格式的命令输出。 选项包括：文本、json。 默认值为 "text"。 （默认值为 "text"）

## <a name="see-also"></a>另请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
