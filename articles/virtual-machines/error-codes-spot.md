---
title: Azure 点 Vm 和规模集实例的错误代码
description: 了解使用污点 Vm 和规模集实例时可能会看到的错误代码。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 1863c510967dcf80044d768aee1a37643a068ce4
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115850"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>预览：专色 Vm 和规模集的错误消息


> [!IMPORTANT]
> 污点 Vm 和虚拟机规模集目前处于公共预览阶段。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


下面是在使用点 Vm 和规模集时可能会收到的一些错误代码。


| Key | Message | 说明 |
|-----|---------|-------------|
| SkuNotAvailable | 资源 "\<资源\>" 的请求层当前在订阅 "\<subscriptionID\>" 的位置 "\<位置\>" 中不可用。 请尝试另一层或部署到其他位置。 | 此位置没有足够的 Azure 污点容量来创建 VM 或规模集实例。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  只能在 Azure 点虚拟机上设置逐出策略。 | 此 VM 不是一个点 VM，因此无法设置逐出策略。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  可用性集中不支持 Azure 点虚拟机。 | 需要选择使用点 VM 或使用可用性集中的 VM，而不能同时选择两者。 |
| AzureSpotFeatureNotEnabledForSubscription  |  未启用 Azure 污点功能的订阅。 | 需要具有支持污点 Vm 的订阅。 |
| VMPriorityCannotBeApplied  |  指定的优先级值 "{0}" 不能应用于虚拟机 "{1}"，因为创建虚拟机时未指定优先级。 | 需要在创建 VM 时指定优先级。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  无法执行操作 "{0}"，因为提供的最大价格 "{1} USD" 低于 Azure 点 VM 大小 "{3}" 的当前污点价格 "{2} USD"。 | 选择较高的最大价格。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价信息。|
| MaxPriceValueInvalid  |  最大价格值无效。 最大价格的唯一支持值为-1 或大于零的小数。 最大价格值-1 表示出于价格原因，不会逐出 Azure 点虚拟机。 | 请输入有效的最大价格。 有关详细信息，请参阅适用于[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | 当前分配 VM "{0}" 时，不允许进行最大价格更改。 请解除分配，然后重试。 | Stop\Deallocate VM，以便可以更改最大价格。 |
| MaxPriceChangeNotAllowed | 不允许最大价格更改。 | 不能更改此 VM 的最大价格。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  此 API 版本不支持 Azure 点。 | API 版本应为2019-03-01。 |
| AzureSpotIsNotSupportedForThisVMSize  |  此 VM 大小 {0}不支持 Azure 点。 | 选择另一个 VM 大小。 有关详细信息，请参阅 "[发现虚拟机](./linux/spot-vms.md)"。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  仅 Azure 点虚拟机支持最大价格。 | 有关详细信息，请参阅 "[发现虚拟机](./linux/spot-vms.md)"。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  移动资源请求包含一个 Azure 点虚拟机。 目前不支持。 请查看虚拟机 Id 的错误详细信息。 | 不能移动专色 Vm。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  移动资源请求包含一个 Azure 点虚拟机规模集。 目前不支持。 请查看虚拟机规模集 Id 的错误详细信息。 | 不能移动位置规模集实例。 |
| EphemeralOSDisksNotSupportedForSpotVMs | 对于污点 Vm，不支持临时 OS 磁盘。 | 需要为你的专色 VM 使用常规 OS 磁盘。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | 虚拟机规模集不支持 Azure 点虚拟机与 VM 业务流程模式。 | 将业务流程模式设置为虚拟机规模集，以便使用位置实例。 |


**后续步骤**有关详细信息，请参阅 "[发现虚拟机](./linux/spot-vms.md)"。