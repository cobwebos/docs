---
title: include 文件
description: include 文件
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81736628"
---
| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 每个订阅的每个区域 Azure Batch 帐户 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 90-900 | 联系支持人员 |
| 每个批处理帐户的低优先级核心数 | 10-100 | 联系支持人员 |
| 每个批处理帐户的**[活动](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作业和作业计划（**完成**的作业无限制） | 100-300 | 1000<sup>1</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>1</sup> |

<sup>1</sup>若要请求增加超出此限制的范围，请联系 Azure 支持。

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 为了帮助我们更好地管理全局运行状况流行病中的容量，某些区域中新批处理帐户和某些类型的订阅的默认核心配额已从上述值（在某些情况下为零个核心）中减少。 创建新的 Batch 帐户时，请[检查核心配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)，并根据需要[请求核心配额增加](../articles/batch/batch-quota-limit.md#increase-a-quota)。 
