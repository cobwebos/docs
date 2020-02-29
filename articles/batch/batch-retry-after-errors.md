---
title: 出错后重试任务
description: 检查是否存在错误，然后重试。
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919985"
---
# <a name="detecting-and-handling-batch-service-errors"></a>检测和处理批处理服务错误

使用 REST 服务 API 时，务必要检查是否有错误。 运行批处理作业时出现错误并不常见。

## <a name="common-errors"></a>常见错误 

- 网络故障-这些请求从不达到批处理或批处理响应未及时到达客户端。
- 内部服务器错误-这些是标准5xx 状态代码 HTTP 响应。
- 阻止会在标头之后出现错误，例如429或503状态代码 HTTP 响应。
- 4xx 错误，其中包括已经存在和 InvalidOperation 之类的错误。 这意味着资源未处于状态转换的正确状态。

有关各种类型的错误代码和特定错误代码的详细信息，请参阅[批处理状态和错误代码](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="when-to-retry"></a>何时重试

如果出现故障，批处理 Api 会通知你。 它们都可以重试，所有这些都包含全局重试处理程序。 最好使用此内置机制。

发生故障后，应等待一点（重试间隔几秒钟），然后重试。 如果重试次数过多或过快，重试处理程序将会中止。

### <a name="for-more-information"></a>更多信息  

[批处理 api 和工具](batch-apis-tools.md)指向 API 参考信息的链接。 例如，.NET API 具有[RetryPolicyProvider 类]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)，其中应指定所需的重试策略。 

有关每个 API 及其默认重试策略的详细信息，请参阅[批状态和错误代码](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。
