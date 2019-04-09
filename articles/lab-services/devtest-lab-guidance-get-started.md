---
title: 使用 Azure 开发测试实验室的热门方案
description: 本文提供有关使用 Azure 开发测试实验室和两个常规路径以开始使用你的组织中的服务的主要方案。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272371"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure 开发测试实验室的热门方案
具体取决于企业的需求，可以配置开发测试实验室，以满足不同的要求。  本文讨论了常用方案。 每个方案介绍如何通过使用开发测试实验室和资源来用于实现这些方案带来的好处。  

- 开发人员桌面
- 测试环境
- 培训课程、 动手实验和编程马拉松
- 沙盒调查
- 教室实验室

## <a name="developer-desktops"></a>开发人员桌面
开发人员针对不同的项目对开发计算机有不同的要求。 使用开发测试实验室，开发人员可以按需虚拟机配置以满足其最常见的方案的访问权限。 开发测试实验室具有以下优点：

- 组织可以提供跨团队确保一致性的常见开发计算机。
- 开发人员可以快速预配按需其开发计算机或[声明现有预配置的计算机](devtest-lab-add-claimable-vm.md)。
- 开发人员可以预配资源的自助服务方式而无需订阅级别的权限。
- IT 或管理员可以[预定义的网络拓扑](devtest-lab-configure-vnet.md)和开发人员可以直接使用它简单且直观的方式而无需任何特殊的访问权限。
- 开发人员可以轻松地[自定义](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)其开发计算机根据需要。
- 管理员可以控制成本，只需确保：
    - 开发人员[无法获取更多 Vm](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)比开发所需的
    - [关闭 Vm](devtest-lab-set-lab-policy.md#set-auto-shutdown)在未使用时
    - 仅[允许的 VM 实例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)面向特定实验室
    - [管理成本目标和通知](devtest-lab-configure-cost-management.md)每个实验室。

有关其他参考资料，请参阅[面向开发人员使用 Azure 开发测试实验室](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>测试环境
在整个企业中创建和管理测试环境可能需要做大量工作。 使用开发测试实验室测试环境可以轻松地创建、 更新，或重复。 它使团队可以在需要时访问的完全配置的环境。 在此方案中，开发测试实验室具有以下优势：

- 组织可以提供跨团队确保一致性的常见测试环境。
- 测试人员可以通过快速预配 Windows 和 Linux 环境，通过使用可重用模板来测试其应用程序的最新版本。
- 管理员可以将实验室连接到 Azure DevOps，以启用 DevOps 方案
- 实验室所有者可以通过确保控制成本：
    - [在环境中的 Vm 会关闭](devtest-lab-set-lab-policy.md#set-auto-shutdown)在未使用时
    - 仅[允许的 VM 实例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)特定实验室
    - [管理成本目标和通知](devtest-lab-configure-cost-management.md)每个实验室。

有关详细信息，请参阅[使用 Azure 开发测试实验室用于 VM 和 PaaS 测试环境](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙盒调查
开发人员通常调查不同的技术或基础结构设计。 默认情况下，使用开发测试实验室中创建的所有环境会都创建其自己的资源组中。 开发测试实验室用户获取对这些资源只能读取访问。 但是，对于开发人员需要更多控制，实验室范围的设置可以进行更新以使[参与者权限](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)到原始的开发测试实验室用户为他们创建的每个环境。  使用开发测试实验室，开发人员可以提供自动向它们在实验室中创建的环境的参与者权限。  这种情况下允许开发人员能够添加和/或更改 Azure 资源，在需要为其开发或测试环境。 [的资源费用](devtest-lab-configure-cost-management.md#view-cost-by-resource)页，使实验室所有者能够跟踪用于调查每个环境的成本。

有关详细信息，请参阅[环境资源组的组访问权限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>培训、 动手实验和编程马拉松 
Azure 开发测试实验室的实验室中充当的很好的容器，如研讨会、 动手实验室、 培训或编程马拉松的暂时性活动。  此服务允许你创建实验室，可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 在此方案中，开发测试实验室具有以下优势：

- [策略](devtest-lab-set-lab-policy.md)确保学员仅获取资源，例如所需的虚拟机数。
- 预配置和创建计算机[索取](devtest-lab-add-claimable-vm.md)与从学员的单个操作。
- 通过访问与学员共享实验室[实验室 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)。
- [到期日期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)虚拟机上确保后不再需要将删除计算机。
- 它很容易[删除实验室](devtest-lab-delete-lab-vm.md#delete-a-lab)和全部[相关的资源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)培训时通过。

有关详细信息，请参阅[使用 Azure 开发测试实验室进行培训](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>与扩展部署的概念证明
一旦决定探索开发测试实验室，有两个常规路径前滚：概念证明概念 vs 按比例缩放部署。  

**规模化部署**包括数周/数月的审查和规划，其目的是将开发测试实验室部署到具有数百或数千个开发人员的整个企业。

一个**概念证明**部署重点介绍从一个团队集中精力来建立组织的值。 虽然想想规模化部署比较诱人，但是该方法通常比概念验证选项更容易失败。 因此，我们建议从小型部署开始，从第一个团队那里学习经验，对两到三个另外的团队重复相同的方法，然后根据所获得的知识规划规模化部署。 为实现成功的概念验证，我们建议你挑选一两个团队，识别其方案（开发环境与测试环境），记录其当前的使用案例，并部署开发测试实验室。

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [开发测试实验室概念](devtest-lab-concepts.md)
- [DevTest Labs 常见问题](devtest-lab-faq.md)

