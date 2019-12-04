---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b671fe9a4ecd35cbe9d70f398f8d39664203fc58
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782107"
---
使用污点 Vm，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

可用容量可能因大小、区域、时间等而有所不同。 部署专色 Vm 时，如果有可用的容量，Azure 将分配 Vm，但这些 Vm 没有 SLA。 点 VM 不提供高可用性保证。 当 Azure 需要恢复容量时，Azure 基础结构会在30秒的时间内逐出点 Vm。 

> [!IMPORTANT]
> 污点实例当前为公共预览版。
> 不建议将此预览版本用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 对于公共预览版的早期部分，污点实例将具有固定价格，因此不会有任何基于价格的逐出。

## <a name="eviction-policy"></a>逐出策略

Vm 可根据容量或设置的最大价格进行逐出。 对于虚拟机，会将逐出策略设置为*解除分配*，这会将逐出的 vm 移到停止解除分配状态，从而使你可以在以后重新部署逐出的 vm。 但是，重新分配点 Vm 将取决于是否存在可用的点容量。 已释放的 Vm 将计入你的 vCPU 配额，你将按你的基础磁盘收费。 

用户可以选择通过[Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md)接收 VM 内通知。 这会在你的 Vm 被逐出时通知你，你将有30秒的时间完成任何作业并在逐出之前执行关闭任务。 

> [!IMPORTANT]
> 对于公共预览版的早期部分，您可以设置最大价格，但会被忽略。 污点 Vm 具有固定价格，因此不会有任何基于价格的逐出。


| 选项 | 业务成效 |
|--------|---------|
| 最大价格设置为 > = 当前价格。 | 如果容量和配额可用，则部署 VM。 |
| 最大价格设置为 < 当前价格。 | VM 未部署。 您将收到一条错误消息，指出最大价格需要 > = 当前价格。 |
| 如果最大价格 > = 当前价格，则重新启动停止/解除分配 VM | 如果有容量和配额，则部署 VM。 |
| 如果最大价格 < 当前价格，则重新启动停止/解除分配 VM | 您将收到一条错误消息，指出最大价格需要 > = 当前价格。 | 
| VM 的价格已开始，现在 > 最大价格。 | VM 将被逐出。 在实际逐出之前，会收到30秒通知。 | 
| 逐出后，VM 的价格会退回 < 最大价格。 | VM 不会自动重新启动。 你可以自行重新启动 VM，并将按当前价格收费。 |
| 如果最大价格设置为 `-1` | 出于定价原因，不会逐出 VM。 最大价格为当前价格，最高可达标准 Vm 的价格。 永远不会按标准价格收费。| 
| 更改最大价格 | 需要解除分配 VM 以更改最大价格。 解除分配 VM，设置新的最大价格，并更新 VM。 |

## <a name="limitations"></a>限制

对于污点 Vm，不支持以下 VM 大小：
 - B 系列
 - 任意大小的促销版本（如 Dv2、NV、NC、H 促销大小）

污点 Vm 当前无法使用临时 OS 磁盘。

污点 Vm 可以部署到任何区域，但 Azure 政府区域中 Microsoft Azure 中国世纪互联和国防部（DoD）除外。

## <a name="pricing"></a>价格

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 


使用可变定价，可以选择设置最大价格（美元），最多可使用5个小数位数。 例如，值 `0.98765`的最大价格为 $0.98765 美元/小时。 如果将最大价格设置为 `-1`，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。


##  <a name="frequently-asked-questions"></a>常见问题

**问：** 创建后，点 VM 与常规标准 VM 是否相同？

**答：** 是，但没有用于发现点 Vm 的 SLA，可以随时将其逐出。


**问：** 当你收回但仍需要容量时，该怎么办？

**答：** 如果需要立即使用容量，建议使用标准 Vm，而不是使用虚拟机。


**问：** 如何为专色 Vm 管理配额？

**答：** 污点 Vm 将有单独的配额池。 将在 Vm 与规模集实例之间共享点配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-subscription-service-limits)。


**问：** 能否为查找附加的配额？

**答：** 是的，你将能够提交请求，以通过[标准配额请求过程](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)增加对污点 vm 的配额。


**问：** 哪些通道支持污点 Vm？

**答：** 有关点 VM 可用性，请参阅下表。

<a name="channel"></a>

| Azure 通道               | Azure 点 Vm 可用性       |
|------------------------------|-----------------------------------|
| 企业协议         | 是                               |
| 即用即付                | 是                               |
| 云服务提供商 (CSP) | [联系你的合作伙伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 优势                     | 不可用                     |
| 赞助                    | 不可用                     |
| 免费试用                   | 不可用                     |


**问：** 可以在何处发布问题？

**答：** 您可以使用 `azure-spot` 的[Q &](https://docs.microsoft.com/answers/topics/azure-spot.html)来发布和标记您的问题。 



