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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736628"
---
| **资源** | **默认限制** | **最大限度** |
| --- | --- | --- |
| 每个订阅每个区域的 Azure 批处理帐户 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 90-900 | 联系支持人员 |
| 每个批处理帐户的低优先级核心数 | 10-100 | 联系支持人员 |
| 每个批处理帐户**[的活动](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作业和作业计划（**已完成**的作业没有限制） | 100-300 | 1，000<sup>1</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>1</sup> |

<sup>1</sup>要请求超出此限制的增幅，请与 Azure 支持部门联系。

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 为了帮助我们在全球卫生大流行期间更好地管理容量，某些区域和某些类型的订阅的新 Batch 帐户的默认核心配额已从上述值范围（在某些情况下为零核心）减少到零。 创建新 Batch 帐户时，[请检查核心配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)并[请求增加核心配额](../articles/batch/batch-quota-limit.md#increase-a-quota)（如果需要）。 
