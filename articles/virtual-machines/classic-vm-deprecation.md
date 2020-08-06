---
title: 我们将于2023年3月1日停用 Azure 经典 Vm
description: 本文概述了经典 VM 停用
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 16ca4ad1204301542ffaa0177d88b2c2800f13b4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836269"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>请于 2023 年 3 月 1 日之前将 IaaS 资源迁移到 Azure 资源管理器 

2014 年，我们在 Azure 资源管理器上启用了 IaaS，此后一直致力于增强相关功能。 由于[azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)现在具有完整的 iaas 功能和其他改进，因此，我们弃用了在2020年2月28日通过[Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic)管理 IaaS vm，此功能将于2023年3月1日完全停用。 

目前，大约有 90% 的 IaaS VM 在使用 Azure 资源管理器。 如果你通过 Azure Service Manager (ASM) 使用 IaaS 资源，现在请开始规划迁移，并在 2023 年 3 月 1 日之前完成迁移，以利用 [Azure 资源管理器](../azure-resource-manager/management/index.yml)。

经典 VM 将按照[新式生命周期策略](https://support.microsoft.com/help/30881/modern-lifecycle-policy)进行停用。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

- 从 2020 年 2 月 28 日开始，在 2020 年 2 月没有通过 Azure Service Manager (ASM) 使用 IaaS VM 的客户将不再能够创建经典 VM。 
- 在 2023 年 3 月 1 日，客户将不再能够使用 Azure Service Manager 启动 IaaS VM，仍在运行的或已分配的任何 IaaS VM 都将停止并解除分配。 
- 2023年3月1日，将通知未迁移到 Azure 资源管理器的订阅有关删除任何剩余经典 Vm 的时间线。  

下列 Azure 服务和功能不受此停用影响  ： 
- 云服务 
- **不**由经典 VM 使用的存储帐户 
- **不**由经典 VM 使用的虚拟网络 (VNet)。 
- 其他经典资源

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？ 

- 立即开始规划到 Azure 资源管理器的迁移。 

- [详细了解](./windows/migration-classic-resource-manager-overview.md)如何将经典 [Linux](./linux/migration-classic-resource-manager-plan.md) 和 [Windows](./windows/migration-classic-resource-manager-plan.md) VM 迁移到 Azure 资源管理器。

- 有关详细信息，请参阅[有关从经典部署模型迁移到 Azure 资源管理器部署模型的常见问题](./windows/migration-classic-resource-manager-faq.md)

- 有关技术问题、问题和向允许列表添加订阅的信息，请[联系支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})人员。

- 对于不属于 FAQ 和反馈的其他问题，请查看下面的注释。

- 尽快完成迁移，以防止业务影响并利用改善的性能、安全性 & Azure 资源管理器提供的新功能。 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>为此迁移提供了哪些资源？

- [Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html)： microsoft & 社区支持迁移

- [Azure 迁移支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在迁移过程中为技术协助提供专用支持团队

- [Microsoft 快速跟踪](https://www.microsoft.com/fasttrack)： Microsoft 快速跟踪团队可以在迁移到符合条件的客户时提供技术帮助。 

- 如果你的公司/组织已与 microsoft 合作，并/或与 Microsoft 代表（如 (云解决方案架构师 (CSA) 、技术客户经理 (Tam) # A5）合作，请与他们合作以获得更多的迁移资源。 

