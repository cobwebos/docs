---
title: Azure Batch 分析 | Microsoft Docs
description: Azure Batch 分析参考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 0f8233846f3835a2bbccf9a7072f223d435b90af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323831"
---
# <a name="batch-analytics"></a>批处理分析
批处理分析中的主题包含可用于批处理服务资源的事件和警报的参考信息。

有关启用和使用批处理诊断日志的详细信息，请参阅 [Azure Batch 诊断日志记录](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)。

## <a name="diagnostic-logs"></a>诊断日志

Azure Batch 服务会在某些批处理资源的生命周期内生成以下诊断日志事件。

**服务日志事件**
* [池创建](batch-pool-create-event.md)
* [池删除启动](batch-pool-delete-start-event.md)
* [池删除完成](batch-pool-delete-complete-event.md)
* [池调整大小启动](batch-pool-resize-start-event.md)
* [池调整大小完成](batch-pool-resize-complete-event.md)
* [任务启动](batch-task-start-event.md)
* [任务完成](batch-task-complete-event.md)
* [任务失败](batch-task-fail-event.md)