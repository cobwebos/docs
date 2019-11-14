---
title: azcopy |Microsoft Docs
description: 本文提供 azcopy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038090"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一个命令行工具，用于将数据移入和移出 Azure 存储。

## <a name="synopsis"></a>概要

命令的常规格式为： `azcopy [command] [arguments] --[flag-name]=[flag-value]`。

若要报告问题或了解有关该工具的详细信息，请参阅[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)。

## <a name="related-conceptual-articles"></a>相关概念文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="options"></a>选项

**--cap-mbps uint32**  以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。

**-h、--help**有关 azcopy 的帮助
      
**--output 类型** 命令输出的格式。 选项包括：文本、json。 默认值为 "text"。 （默认值为 "text"）

## <a name="see-also"></a>另请参阅

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [azcopy 工作台](storage-ref-azcopy-bench.md)
- [azcopy 副本](storage-ref-azcopy-copy.md)
- [azcopy 文档](storage-ref-azcopy-doc.md)
- [azcopy 环境](storage-ref-azcopy-env.md)
- [azcopy 作业](storage-ref-azcopy-jobs.md)
- [azcopy 作业清理](storage-ref-azcopy-jobs-clean.md)
- [azcopy 作业列表](storage-ref-azcopy-jobs-list.md)
- [删除作业 azcopy](storage-ref-azcopy-jobs-remove.md)
- [azcopy 作业恢复](storage-ref-azcopy-jobs-resume.md)
- [azcopy 作业显示](storage-ref-azcopy-jobs-show.md)
- [azcopy 列表](storage-ref-azcopy-list.md)
- [azcopy 登录](storage-ref-azcopy-login.md)
- [azcopy 注销](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy 删除](storage-ref-azcopy-remove.md)
- [azcopy 同步](storage-ref-azcopy-sync.md)
