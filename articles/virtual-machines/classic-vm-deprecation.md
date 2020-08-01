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
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461198"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>请于 2023 年 3 月 1 日之前将 IaaS 资源迁移到 Azure 资源管理器 

2014 年，我们在 Azure 资源管理器上启用了 IaaS，此后一直致力于增强相关功能。 由于 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)现在具有完整的 IaaS 功能和其他改进，因此，我们在 2020 年 2 月 28 日弃用了通过 Azure Service Manager 管理 IaaS VM 的功能，此功能将于 2023 年 3 月 1 日完全停用。 

目前，大约有 90% 的 IaaS VM 在使用 Azure 资源管理器。 如果你通过 Azure Service Manager (ASM) 使用 IaaS 资源，现在请开始规划迁移，并在 2023 年 3 月 1 日之前完成迁移，以利用 [Azure 资源管理器](../azure-resource-manager/management/index.yml)。

经典 VM 将按照[新式生命周期策略](https://support.microsoft.com/help/30881/modern-lifecycle-policy)进行停用。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

1) 从 2020 年 2 月 28 日开始，在 2020 年 2 月没有通过 Azure Service Manager (ASM) 使用 IaaS VM 的客户将不再能够创建经典 VM。 
2) 在 2023 年 3 月 1 日，客户将不再能够使用 Azure Service Manager 启动 IaaS VM，仍在运行的或已分配的任何 IaaS VM 都将停止并解除分配。 
2) 在 2023 年 3 月 1 日，尚未迁移到 Azure 资源管理器的订阅将收到通知，其中将提供有关删除剩余的经典 VM 的时间线。  

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

- 若要在迁移过程中获得帮助，[请联系迁移支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})

- 对于不属于 FAQ 和反馈的其他问题，请查看下面的注释。
