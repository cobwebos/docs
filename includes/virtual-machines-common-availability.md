---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748914"
---
本文提供 Azure 虚拟机（Vm）的可用性功能的概述。

## <a name="high-availability"></a>고가용성

工作负载通常分布在不同的虚拟机上，以获得高吞吐量、性能和创建冗余，以防 VM 因更新或其他事件而受到影响。 

Azure 提供了几个选项来实现高可用性。 首先，让我们谈一谈基本构造。 

### <a name="availability-zones"></a>가용성 영역

"[可用性区域](../articles/availability-zones/az-overview.md)" 展开控制级别，你必须在 vm 上维护应用程序和数据的可用性。 可用性区域是 Azure 区域中物理上独立的区域。 지원되는 Azure 지역당 3개의 가용성 영역이 있습니다. 

각 가용성 영역에는 고유한 소스, 네트워크 및 냉각 장치가 있습니다. 영역에 복제된 VM을 사용하는 솔루션을 설계하여 데이터 센터 손실로부터 앱과 데이터를 보호할 수 있습니다. 하나의 영역이 손상되면 다른 영역에서 복제된 앱 및 데이터를 즉시 사용할 수 있습니다. 

![가용성 영역](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

가용성 영역에서 [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) 또는 [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM을 배포하는 방법에 대해 자세히 알아봅니다.


### <a name="fault-domains"></a>장애 도메인

장애 도메인은 온-프레미스 데이터 센터 내의 랙과 비슷하게 공통 전원 및 네트워크 스위치를 공유하는 기본 하드웨어의 논리적 그룹입니다. 

### <a name="update-domains"></a>업데이트 도메인

업데이트 도메인은 동시에 유지 관리를 진행하거나 다시 부팅될 수 있는 기본 하드웨어의 논리적 그룹입니다. 

이 방법을 통해 Azure 플랫폼이 정기적으로 유지 관리를 거치는 동안 애플리케이션에 있는 하나 이상의 인스턴스가 항상 실행됩니다. 在维护期间，重新启动更新域的顺序可能不会按序进行，但一次只能重启一个更新域。


## <a name="virtual-machines-scale-sets"></a>Virtual Machines 크기 집합 

使用 Azure 虚拟机规模集，可以创建和管理一组负载均衡的 Vm。 VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 规模集为应用程序提供高可用性，并允许你集中管理、配置和更新多个虚拟机。 建议在规模集中创建两个或更多 Vm，以提供高度可用的应用程序并满足[99.95% 的 AZURE SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)要求。 规模集本身无需支付费用，只需为创建的每个 VM 实例付费。 단일 VM이 [Azure 프리미엄 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다. 规模集中的虚拟机可以跨多个更新域和容错域部署，以最大程度地提高可用性和可复原性，因为数据中心中断、计划内或计划外维护事件。 规模集中的虚拟机也可以部署到单个可用性区域或突破中。 可用性区域部署选项可能因业务流程模式而异。

### <a name="preview-orchestration-mode-preview"></a>预览：业务流程模式预览
虚拟机规模集允许指定业务流程模式。  利用虚拟机规模集业务流程模式（预览版），你现在可以选择规模集是否应协调在规模集配置模型外显式创建的虚拟机，或是否隐式创建的虚拟机实例基于配置模型。 选择 "业务流程" 模式： VM 业务流程模型允许您将显式定义的虚拟机组合到一个区域或某个可用性区域中。 在可用性区域中部署的虚拟机为 Vm 提供了区域隔离，它们被绑定到可用性区域边界，而不是在区域的其他可用性区域中可能发生的任何故障。 

|   | "orchestrationMode"： "VM" （VirtualMachine）| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| VM 配置模型| 없음. 规模集模型中未定义 VirtualMachineProfile。 | 필수 사항입니다. VirtualMachineProfile 在规模集模型中填充。 |
| 向规模集添加新 VM| 创建 VM 时，Vm 会显式添加到规模集。 | Vm 基于 VM 配置模型、实例计数和自动缩放规则进行隐式创建并添加到规模集。 |
| 가용성 영역| 支持一个可用性区域中的区域部署或 Vm| 支持区域部署或多个可用性区域;可以定义区域平衡策略 |
| 장애 도메인| 可定义容错域计数。 2或3，基于区域支持，5用于可用性区域。 已分配的 VM 容错域将保留 VM 生命周期，包括解除分配和重新启动。 | 可为非区域性部署定义1、2或3个容错域，并为可用性区域部署定义5个。 分配的 VM 容错域不会保留在 VM 的生命周期中，在分配时，会为虚拟机分配一个容错域。 |
| 업데이트 도메인| 해당 없음. 更新域会自动映射到容错域| 해당 없음. 更新域会自动映射到容错域 |

**容错域和更新域**

虚拟机规模集通过协调容错域和更新域简化了高可用性设计。 只需为规模集定义容错域计数。 规模集可用的容错域的数量可能因区域而异。 请参阅[在 Azure 中管理虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。


## <a name="availability-sets"></a>가용성 집합
가용성 세트은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 데이터 센터에 있는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다. 단일 VM이 [Azure 프리미엄 SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다.

在可用性集中，Vm 自动分布到这些容错域中。 이 방법은 잠재적인 물리적 하드웨어 오류, 네트워크 중단 또는 전원 중단의 영향을 제한합니다.

[Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md)를 사용하는 VM의 경우, 관리 가용성 집합을 사용할 때 VM은 관리 디스크 장애 도메인에 맞춰집니다. 이러한 정렬은 VM에 연결된 모든 관리 디스크가 동일한 관리 디스크 장애 도메인 내에 있도록 합니다. 

관리 디스크의 VM만 관리 가용성 집합에서 만들어질 수 있습니다. 관리 디스크 장애 도메인의 수는 지역에 따라 다릅니다. 즉, 지역당 2개 또는 3개의 관리 디스크 장애 도메인이 있을 수 있습니다. 이러한 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 또는 [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)용 관리 디스크 장애 도메인에 대한 자세한 내용을 참조할 수 있습니다.

![관리 가용성 집합](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


可用性集中的 Vm 也会自动分布在更新域中。 

![가용성 집합](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>다음 단계
이제 이러한 가용성 및 중복 기능을 사용하여 Azure 환경을 빌드하기 시작할 수 있습니다. 모범 사례 정보는 [Azure 가용성 모범 사례](/azure/architecture/checklist/resiliency-per-service)를 참조하세요.

