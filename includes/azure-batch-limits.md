---
title: include 文件
description: include 文件
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "67080894"
---
| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 每个订阅的每个区域 Azure Batch 帐户 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 90-900 | 联系技术支持 |
| 每个批处理帐户的低优先级核心数 | 10-100 | 联系技术支持 |
| 每个批处理帐户的 **[活动](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作业和作业计划（**完成**的作业无限制） | 100-300 | 1,000<sup>1</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

<sup>1</sup>若要请求增加超出此限制的范围，请联系 Azure 支持。
