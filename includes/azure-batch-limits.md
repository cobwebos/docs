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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080894"
---
| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 每个区域每个订阅的 azure Batch 帐户 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 90-900 | 联系支持人员 |
| 每个批处理帐户的低优先级核心数 | 10-100 | 联系支持人员 |
| **[Active](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作业和作业计划每个批处理帐户 (**完成**作业具有无限制) | 100-300 | 1,000<sup>1</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

<sup>1</sup>若要请求增加的配额超出此限制，请联系 Azure 支持部门。
