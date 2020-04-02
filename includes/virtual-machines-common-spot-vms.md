---
title: include 文件
description: include 文件
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d56964b7415e4ca5903950cd46c02b3c27f62d5e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547410"
---
使用 Spot VM 使您能够利用我们未使用的容量，从而显著节省成本。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将驱逐 Spot VM。 因此，Spot VM 非常适合处理批处理作业、开发/测试环境、大型计算工作负载等中断的工作负载。

可用容量的数量可能因大小、区域、一天中的时间等而异。 部署 Spot VM 时，如果可用容量，但这些 VM 没有 SLA，Azure 将分配 VM。 Spot VM 不提供高可用性保证。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将在 30 秒通知后逐出 Spot VM。 


## <a name="eviction-policy"></a>驱逐政策

VM 可以根据容量或您设置的最大价格被逐出。 对于虚拟机，逐出策略设置为 *"取消分配*"，这将逐出 VM 移动到已停止的已停止的已停止的已停止位置状态，从而允许您在以后重新部署已展开的 VM。 但是，重新分配 Spot VM 将取决于存在可用的 Spot 容量。 交易定位的 VM 将计入您的现场 vCPU 配额，您将为基础磁盘付费。 

用户可以选择加入通过[Azure 计划事件](../articles/virtual-machines/linux/scheduled-events.md)接收 VM 内通知。 如果 VM 被逐出，您将通知您，并且在驱逐之前，您将有 30 秒的时间完成任何作业并执行关闭任务。 


| 选项 | 业务成效 |
|--------|---------|
| 最高价格设置为>= 当前价格。 | 如果容量和配额可用，则部署 VM。 |
| 最高价格设置为<当前价格。 | 未部署 VM。 您会收到一条错误消息，指出最高价格需要>= 当前价格。 |
| 如果最大价格>= 当前价格，则重新启动停止/取消分配 VM | 如果有容量和配额，则部署 VM。 |
| 如果最高价格<当前价格，则重新启动停止/取消分配 VM | 您会收到一条错误消息，指出最高价格需要>= 当前价格。 | 
| VM 的价格已经上涨，现在>最高价格。 | VM 将被逐出。 在实际驱逐之前，您会收到 30s 的通知。 | 
| 驱逐后，VM 的价格会回到<最高价格。 | 不会自动重新启动 VM。 您可以自行重新启动 VM，并将按当前价格收费。 |
| 如果最高价格设置为`-1` | 出于定价原因，不会逐出 VM。 最高价格将是当前价格，最高为标准 VM 的价格。 您永远不会被收取高于标准价格的费用。| 
| 更改最高价格 | 您需要取消分配 VM 以更改最大价格。 取消分配 VM，设置新的最高价格，然后更新 VM。 |

## <a name="limitations"></a>限制

Spot VM 不支持以下 VM 大小：
 - B 系列
 - 任何尺寸的促销版本（如 Dv2、NV、NC、H 促销尺寸）

Spot VM 当前无法使用临时操作系统磁盘。

除 Microsoft Azure 中国 21Vianet 外，Spot VM 都可以部署到任何区域。

## <a name="pricing"></a>定价

现货 VM 的定价基于区域和 SKU 是可变的。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 


使用可变定价，您可以选择使用最多 5 个小数位设置最高价格（美元 （USD）。 例如，该值`0.98765`将是每小时 0.98765 美元的最高价格。 如果将最高价格设置为`-1`，则 VM 不会根据价格被逐出。 VM 的价格将是现货的当前价格或标准 VM 的价格，只要有容量和配额可用，标准 VM 的价格就更少了。


##  <a name="frequently-asked-questions"></a>常见问题

**问：** 创建后，Spot VM 是否与常规标准 VM 相同？

**答：** 是，除了 Spot VM 没有 SLA，它们可以随时被逐出。


**问：** 当您被逐出时该怎么办，但仍需要容量？

**答：** 如果您需要容量，我们建议您使用标准 VM 而不是 Spot VM。


**问：** 如何管理 Spot VM 的配额？

**答：** Spot VM 将具有单独的配额池。 竞价配额将在 VM 和缩放设置实例之间共享。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**问：** 我可以申请 Spot 的额外配额吗？

**答：** 是的，您将能够通过[标准配额请求流程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)提交请求，以增加 Spot VM 的配额。


**问：** 哪些通道支持 Spot VM？

**答：** 有关 Spot VM 可用性，请参阅下表。

<a name="channel"></a>

| Azure 通道               | Azure Spot VM 可用性       |
|------------------------------|-----------------------------------|
| 企业协议         | 是                               |
| Pay As You Go                | 是                               |
| 云服务提供商 (CSP) | [联系您的合作伙伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 优点                     | 不可用                     |
| 赞助                    | 不可用                     |
| 免费试用版                   | 不可用                     |


**问：** 我在哪里可以发布问题？

**答：** 您可以在[Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)`azure-spot`上发布和标记您的问题。 



