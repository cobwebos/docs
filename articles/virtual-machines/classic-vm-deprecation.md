---
title: 我们将于2023年3月1日停用 Azure 经典 Vm
description: 本文提供经典 VM 停用的高级概述
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127334"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023年3月1日将 IaaS 资源迁移到 Azure 资源管理器 

在2014中，我们在 Azure 资源管理器上启动了 IaaS，并使其能够在以后增强功能。 由于[azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)现在具有完整的 iaas 功能和其他改进，因此，我们弃用了在2020年2月28日通过 Azure Service Manager 管理 IaaS vm，此功能将于2023年3月1日完全停用。 

如今，大约90% 的 IaaS Vm 正在使用 Azure 资源管理器。 如果通过 Azure Service Manager （ASM）使用 IaaS 资源，请立即开始规划迁移并在2023年3月1日完成迁移，以利用[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/)。

经典 Vm 将遵循[现代化生命周期策略](https://support.microsoft.com/help/30881/modern-lifecycle-policy)。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

1) 从2020年2月28日开始，未通过 Azure 使用 IaaS Vm 的客户将无法再使用 Azure Service Manager （ASM 2020）来创建经典 Vm。 
2) 2023年3月1日，客户将无法再使用 Azure Service Manager 启动 IaaS Vm，并且仍在运行或分配的任何都将停止并解除分配。 
2) 2023年3月1日，尚未迁移到 Azure 资源管理器的订阅将收到有关删除任何剩余经典 Vm 的时间线。  

下列 Azure 服务和功能不受此停用影响： 
- 云服务 
- 经典 Vm**未**使用的存储帐户 
- 经典 Vm**未**使用的虚拟网络（vnet）。 
- 其他经典资源

## <a name="what-actions-should-i-take"></a>我应该执行哪些操作？ 

- 立即开始规划迁移到 Azure 资源管理器。 

- [详细了解](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)如何将经典[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) vm 迁移到 Azure 资源管理器。

- 有关详细信息，请参阅[有关经典到 Azure 资源管理器迁移](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)的常见问题

- 有关技术问题和问题，请[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

- 对于不属于 FAQ 和反馈的其他问题，请查看下面的注释。
