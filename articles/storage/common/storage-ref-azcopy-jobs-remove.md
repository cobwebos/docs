---
title: 删除作业 azcopy |Microsoft Docs
description: 本文提供 azcopy job remove 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034160"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

删除与给定的作业 ID 关联的所有文件。

> [!NOTE] 
> 您可以自定义日志和计划文件的保存位置。 若要了解详细信息，请参阅[azcopy env](storage-ref-azcopy-env.md)命令。

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>选项

**-h、--help**               删除帮助。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32**     以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。

**--输出类型**字符串格式的命令输出。 选项包括：文本、json。 默认值为 "text"。 （默认值为 "text"）

## <a name="see-also"></a>另请参阅

- [azcopy 作业](storage-ref-azcopy-jobs.md)
