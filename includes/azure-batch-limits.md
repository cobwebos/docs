---
title: include 文件
description: include 文件
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127545"
---
| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 每个区域每个订阅的 azure Batch 帐户 | 1-3 |50 |
| 每个批处理帐户的专用核心数 | 10-100 | 不适用<sup>1</sup> |
| 每个批处理帐户的低优先级核心数 | 10-100 | N/A<sup>2</sup> |
| 每个批处理帐户的活动作业和作业计划数<sup>3</sup> | 100-300 | 1,000<sup>4</sup> |
| 每个 Batch 帐户的池数 | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> 默认限制因用于创建 Batch 帐户的订阅类型而异。 显示的核心配额适用于 Batch 服务模式下的 Batch 帐户。 [查看 Batch 帐户中的配额](../articles/batch/batch-quota-limit.md#view-batch-quotas)。 

<sup>1</sup>可以增加的每个批处理帐户的专用核心数，但未指定最大数目。 若要讨论增加选项，请联系 Azure 支持。

<sup>2</sup>可以增加每个批处理帐户的低优先级核心数，但不指定最大数目。 若要讨论增加选项，请联系 Azure 支持。

<sup>3</sup>已完成的作业和作业计划不受限制。

<sup>4</sup>若要请求增加的配额超出此限制，请联系 Azure 支持部门。
