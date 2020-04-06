---
title: 我们将在 2023 年 3 月 1 日停用 Azure 经典 VM
description: 本文提供了经典 VM 停用的高级概述
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668805"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>到 2023 年 3 月 1 日前将 IaaS 资源迁移到 Azure 资源管理器 

2014 年，我们在 Azure 资源管理器上推出了 IaaS，并自那时以来一直在增强功能。 由于[Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)现在具有完整的 IaaS 功能和其他进步，因此我们于 2020 年 2 月 28 日通过 Azure 服务管理器弃用 IaaS VM 的管理，此功能将于 2023 年 3 月 1 日完全停用。 

如今，大约 90% 的 IaaS VM 正在使用 Azure 资源管理器。 如果通过 Azure 服务管理器 （ASM） 使用 IaaS 资源，请立即开始规划迁移，并在 2023 年 3 月 1 日前完成迁移，以利用[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/)。

经典 VM 将遵循[现代生命周期政策](https://support.microsoft.com/help/30881/modern-lifecycle-policy)进行退休。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

1) 从 2020 年 2 月 28 日起，未在 2020 年 2 月通过 Azure 服务管理器 （ASM） 使用 IaaS VM 的客户将无法再创建经典 VM。 
2) 2023 年 3 月 1 日，客户将不再能够使用 Azure 服务管理器启动 IaaS VM，任何仍在运行或分配的用户都将停止并进行处理。 
2) 2023 年 3 月 1 日，将通知尚未迁移到 Azure 资源管理器的订阅删除任何剩余的经典 VM 的时间表。  

以下 Azure 服务和**功能不会受**此停用的影响： 
- 云服务 
- 经典 VM**未使用的**存储帐户 
- 经典 VM**未使用的**虚拟网络 （VNet）。 
- 其他经典资源

## <a name="what-actions-should-i-take"></a>我应该采取什么行动？ 

- 立即开始规划迁移到 Azure 资源管理器。 

- [详细了解](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)如何将经典[Linux](./linux/migration-classic-resource-manager-plan.md)和[Windows](./windows/migration-classic-resource-manager-plan.md) VM 迁移到 Azure 资源管理器。

- 有关详细信息，请参阅有关 Azure[资源管理器迁移的经典问题](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- 对于技术问题、问题和订阅白名单[，请联系支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

- 对于不属于常见问题解答和反馈的其他问题，请评论如下。
