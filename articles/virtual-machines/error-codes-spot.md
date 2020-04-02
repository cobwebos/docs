---
title: Azure Spot VM 和缩放集实例的错误代码
description: 了解在使用 Spot VM 和缩放集实例时可能看到的错误代码。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547814"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Spot VM 和缩放集的错误消息

以下是使用 Spot VM 和缩放集时可以接收的一些可能的错误代码。


| 键 | 消息 | 说明 |
|-----|---------|-------------|
| SkuNotAvailable | 资源"资源\<\>"的请求层当前不在位置"\<位置\>"，用于订阅"\<订阅 ID"。\> 请尝试其他层或部署到其他位置。 | 此位置没有足够的 Azure Spot 容量来创建 VM 或缩放集实例。 |
| 驱逐策略只能设置 AzureSpot 虚拟机  |  只能在 Azure Spot 虚拟机上设置逐出策略。 | 此 VM 不是 Spot VM，因此无法设置逐出策略。 |
| AzureSpotVM 不支持的可用性集  |  可用性集中不支持 Azure Spot 虚拟机。 | 您需要选择使用 Spot VM 或在可用性集中使用 VM，不能同时选择这两种功能。 |
| 未启用订阅的 Azure Spot 功能  |  未使用 Azure Spot 功能启用订阅。 | 使用支持 Spot VM 的订阅。 |
| VM 优先级不可应用  |  指定的优先级值 ''{0}' 不能应用于虚拟机'{1}， 因为创建虚拟机时未指定优先级。 | 指定创建 VM 时的优先级。 |
| 现货价格大于提供最大价格  |  无法执行{0}操作''，因为提供的最大价格 "USD"{1}低于 Azure Spot VM{2}大小的当前即期价格"美元"。{3} | 选择较高的最高价格。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价信息。|
| 最大价格值无效  |  无效的最大值。 最大价格的唯一支持值是 -1 或大于零的小数。 最高价格值 -1 表示 Azure Spot 虚拟机不会出于价格原因被逐出。 | 输入有效最高价格。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价。 |
| 对于分配的 VM，不允许使用最大价格更改 | 当前分配 VM '{0}时，不允许更改最大价格。 请取消分配，然后重试。 | 停止\分配 VM，以便您可以更改最大价格。 |
| 最大价格更改不允许 | 不允许最大价格变化。 | 您不能更改此 VM 的最大价格。 |
| AzureSpotis 不支持此 API 版本  |  此 API 版本不支持 Azure Spot。 | API 版本需要为 2019-03-01。 |
| AzureSpotis 不支持此 VMSize  |  此 VM 大小{0}不支持 Azure Spot。 | 选择另一个 VM 大小。 有关详细信息，请参阅[Spot 虚拟机](./linux/spot-vms.md)。 |
| 仅支持 AzureSpot 虚拟机的 MaxPriceis  |  仅支持 Azure Spot 虚拟机的最大价格。 | 有关详细信息，请参阅[Spot 虚拟机](./linux/spot-vms.md)。 |
| 移动资源，不支持 AzureSpotVM  |  移动资源请求包含 Azure Spot 虚拟机。 目前不支持。 请检查虚拟机 ID 的错误详细信息。 | 不能移动 Spot VM。 |
| 移动资源与 AzureSpotVms 不支持  |  移动资源请求包含 Azure Spot 虚拟机缩放集。 目前不支持。 请检查虚拟机规模设置 Id 的错误详细信息。 | 不能移动"点缩放集实例"。 |
| 临时OSDisk不支持SpotVM | Spot VM 不支持临时操作系统磁盘。 | 为 Spot VM 使用常规操作系统磁盘。 |
| AzureSpotVM 不支持在 VM 中使用 VM 编排模式 | 使用 VM 业务流程模式的虚拟机缩放集不支持 Azure Spot 虚拟机。 | 将业务流程模式设置为虚拟机缩放集，以便使用 Spot 实例。 |


**后续步骤**有关详细信息，请参阅[现场虚拟机](./linux/spot-vms.md)。