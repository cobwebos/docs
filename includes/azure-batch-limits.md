---
title: include 文件
description: include 文件
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 8d23da1c3a45ed12193dfd4c1fc2ede453fc6ac7
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323870"
---
| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 每个区域每个订阅的 Azure Batch 帐户数 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 90-900 | 联系支持人员 |
| 每个批处理帐户的低优先级核心数 | 10-100 | 联系支持人员 |
| 每个 Batch 帐户的 **[活动](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作业和作业计划（**已完成**的作业没有任何限制） | 100-300 | 1,000<sup>1</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>1</sup> |

<sup>1</sup> 若要请求增加到超出此限制，请与 Azure 支持部门联系。

> [!IMPORTANT]
> 我们正在更改专用配额的请求和管理方式。  专用 vCPU 总数是当前强制实施的值，但很快就会强制实施每 VM 系列的专用配额。 低优先级配额将继续基于总限额强制实施；它不会按 VM 系列强制实施。

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 为了帮助我们在全球疫情期间更好地管理容量，某些区域中新 Batch 帐户的默认核心配额和某些类型的订阅的默认核心配额已从上述范围的值（在某些情况下）减少到零个核心。 创建新的 Batch 帐户时，请[检查核心配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)并[请求增加核心配额](../articles/batch/batch-quota-limit.md#increase-a-quota)（如果需要）。 
