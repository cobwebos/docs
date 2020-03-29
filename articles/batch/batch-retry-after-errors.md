---
title: 错误后重试任务
description: 检查错误并重试。
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919985"
---
# <a name="detecting-and-handling-batch-service-errors"></a>检测和处理批处理服务错误

请务必记住，在使用 REST 服务 API 时，应检查错误。 运行批处理作业时发生错误的情况并不少见。

## <a name="common-errors"></a>常见错误 

- 网络故障 - 这些请求从未到达批处理或批处理响应未及时到达客户端。
- 内部服务器错误 - 这些是标准 5xx 状态代码 HTTP 响应。
- 限制可能会导致错误，如 429 或 503 状态代码 HTTP 响应与重试后标头。
- 包含"已存在"和"无效操作"等错误的 4xx 错误。 这意味着资源未处于状态转换的正确状态。

有关各种类型错误代码和特定错误代码的详细信息，请参阅[批次状态和错误代码](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="when-to-retry"></a>何时重试

如果发生故障，批处理 API 将通知您。 它们都可以重试，并且它们都包括用于此目的的全局重试处理程序。 最好使用此内置机制。

失败后，您应该等待一段时间（重试之间的几秒钟），然后再重试。 如果重试过于频繁或过快，重试处理程序将限制。

### <a name="for-more-information"></a>更多信息  

[批处理 API 和工具](batch-apis-tools.md)链接到 API 参考信息。 例如，.NET API 具有[重试策略提供程序类]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)，其中应指定所需的重试策略。 

有关每个 API 及其默认重试策略的详细信息，请阅读[批处理状态和错误代码](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。
