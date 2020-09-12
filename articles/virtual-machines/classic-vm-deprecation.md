---
title: 正在淘汰2023年3月1日 (经典) 的 Azure Vm
description: 本文对使用经典部署模型创建的 Vm 的停用进行了高级概述。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7f2db507176d65e7794607e83db8605b2f892c1c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646636"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>请于 2023 年 3 月 1 日之前将 IaaS 资源迁移到 Azure 资源管理器 

在2014中，我们在 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)上启动了基础结构即服务 (IaaS) 。 自以来，我们一直在增强功能。 由于 Azure 资源管理器现在具有完整的 IaaS 功能和其他改进，因此，我们不推荐在2020年2月28日通过 [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) 管理 iaas 虚拟机 (vm) 。 此功能将在2023年3月1日完全停用。 

如今，大约90% 的 IaaS Vm 正在使用 Azure 资源管理器。 如果通过 ASM 使用 IaaS 资源，请立即开始规划迁移。 请在2023年3月1日完成，以利用 [Azure 资源管理器](../azure-resource-manager/management/index.yml)。

使用经典部署模型创建的 Vm 将遵循用于停用的 [新式生命周期策略](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

- 从2020年2月28日开始，在2020年2月之前未通过 ASM 使用 IaaS Vm 的客户将无法再创建 (经典) 的 Vm。 
- 2023年3月1日，客户将无法再使用 ASM 启动 IaaS Vm。 仍在运行或分配的任何都将停止并解除分配。 
- 2023年3月1日，将通知未迁移到 Azure 资源管理器的订阅有关删除任何剩余 Vm (经典) 的时间线。  

此停用 *不会影响以下* Azure 服务和功能： 
- Azure 云服务 
- Vm *未* 使用的存储帐户 (经典)  
- Vm *未* 使用的虚拟网络 (经典)  
- 其他经典资源

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？ 

立即开始规划到 Azure 资源管理器的迁移。 

1. 列出所有受影响的 Vm： 

   - [AZURE 门户 vm 窗格](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)中** (经典) 类型的虚拟机**的 vm 是订阅中所有受影响的 vm。 
   - 你还可以使用 [门户](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) 或 [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) 查询 Azure 资源关系图，以查看所选订阅 (经典) 和相关信息的所有已标记 vm 的列表。 
   - 2020年2月8日和9月2日，我们向订阅所有者发送电子邮件，其中包含包含这些 Vm (经典) 的所有订阅的列表。 请使用它们来生成此列表。 

1. [了解](./windows/migration-classic-resource-manager-overview.md) 有关将 [Linux](./linux/migration-classic-resource-manager-plan.md) 和 [Windows](./windows/migration-classic-resource-manager-plan.md) Vm (经典) 迁移到 Azure 资源管理器的详细信息。 有关详细信息，请参阅 [有关经典到 Azure 资源管理器迁移](./migration-classic-resource-manager-faq.md)的常见问题。

1. 建议使用 [平台支持迁移工具](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) 来迁移现有 vm，只需执行以下三个步骤：验证、准备和提交。 该工具旨在最大程度地迁移 Vm，无需停机。 

   1. 第一步是验证，不会影响现有的部署，并提供所有不受支持的迁移方案的列表。 
   1. 完成解决 [方法的列表](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) 以修复部署并使其做好迁移准备。 
   1. 理想情况下，在修复所有验证错误后，在准备和提交步骤期间不会遇到任何问题。 提交成功后，你的部署将实时迁移到 Azure 资源管理器，然后即可通过 Azure 资源管理器公开的新 Api 进行管理。 

   如果迁移工具不适合您的迁移，则可以浏览 [其他计算产品](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) 以进行迁移。 由于有许多 Azure 计算产品，它们彼此不同，我们无法为其提供平台支持的迁移路径。  

1. 有关向允许列表添加订阅的技术问题、问题和帮助，请 [联系支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})人员。

1. 尽快完成迁移，以防止业务影响，并利用 Azure 资源管理器的改进性能、安全性和新功能。 

## <a name="what-resources-are-available-for-this-migration"></a>可用于此迁移的资源有哪些？

- [Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html)：用于迁移的 microsoft 和社区支持。

- [Azure 迁移支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在迁移过程中为技术协助提供专用支持团队。

- [Microsoft 快速跟踪](https://www.microsoft.com/fasttrack)：可在迁移到符合条件的客户过程中提供技术帮助的团队。 

如果你的公司/组织已与 Microsoft 合作，或与 Microsoft 代表 (如云解决方案架构师 (Csa) 或技术客户经理 (Tam) # 

