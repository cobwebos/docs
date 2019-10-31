---
title: 详细了解 Azure 中虚拟机规模集的业务流程模式
description: 详细了解 Azure 中虚拟机规模集的业务流程模式。
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: gwallace
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 063b3210877c06edf7eeddab37c50ed84033098a
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065907"
---
# <a name="orchestration-mode-preview"></a>业务流程模式（预览）

虚拟机规模集提供平台托管的虚拟机的逻辑分组。 使用规模集，可以创建虚拟机配置模型，根据 CPU 或内存负载自动添加或删除其他实例，并自动升级到最新的操作系统版本。 在传统上，规模集允许你使用在创建规模集时提供的 VM 配置模型来创建虚拟机，并且规模集只能管理基于配置模型隐式创建的虚拟机。

使用规模集业务流程模式（预览版），你现在可以选择规模集是否应协调在规模集配置模型之外显式创建的虚拟机，或基于配置模型。 规模集协调模式还通过将这些 Vm 部署在容错域和可用性区域中，帮助你设计用于实现高可用性的 VM 基础结构。


虚拟机规模集将支持2种不同的业务流程模式：

- ScaleSetVM –添加到规模集的虚拟机实例基于规模集配置模型。 虚拟机实例生命周期-创建、更新、删除-由规模集管理。
- VM （虚拟机）–可以将在规模集外创建的虚拟机显式添加到规模集中。 
 

> [!IMPORTANT]
> 业务流程模式是在创建规模集时定义的，不能在以后更改或更新。 
> 
> 虚拟机规模集的此功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="orchestration-modes"></a>业务流程模式

|                             | "orchestrationMode"： "VM" （VirtualMachine） | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| VM 配置模型      | None                                       | 需要 |
| 向规模集添加新 VM  | 创建 VM 时，Vm 会显式添加到规模集。 | Vm 基于 VM 配置模型、实例计数和自动缩放规则进行隐式创建并添加到规模集 | |
| 删除 VM                   | 要单独删除 Vm，如果规模集包含任何 Vm，则不会将其删除。 | 可单独删除 Vm，删除规模集将删除所有 VM 实例。  |
| 附加/分离 Vm           | 不支持                              | 不支持 |
| 实例生命周期（通过删除创建） | Vm 及其项目（如磁盘和 Nic）可以单独进行管理。 | 实例及其项目（如磁盘和 Nic）对创建它们的规模集实例是隐式的。 它们不能在规模集外单独分离或管理 |
| 容错域               | 可定义容错域。 2或3，基于区域支持，5用于可用性区域。 | 可以定义从1到5的容错域 |
| 更新域              | 更新域会自动映射到容错域 | 更新域会自动映射到容错域 |
| 可用性区域          | 支持一个可用性区域中的区域部署或 Vm | 支持区域部署或多个可用性区域;可以定义区域平衡策略 |
| 自动扩展                   | 不支持                              | 受支持 |
| OS 升级                  | 不支持                              | 受支持 |
| 模型更新               | 不支持                              | 受支持 |
| 实例控件            | 完全 VM 控件。 Vm 具有完全限定的 URI，支持全套 Azure VM 管理功能（如 Azure 策略、Azure 备份和 Azure Site Recovery） | Vm 是规模集的依赖资源。 仅可通过规模集访问实例以进行管理。 |
| 实例模型              | VirtualMachines/模型定义。 | VirtualMachineScaleSets/VirtualMachines 模型定义。 |
| Capacity                    | 可以创建空规模集;最多可将200个 Vm 添加到规模集 | 可以使用实例计数 0-1000 定义规模集 |
| 移动                        | 受支持                                  | 受支持 |
| 单个放置组 = = false | 不支持                          | 受支持 |


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[可用性选项概述](availability.md)。
