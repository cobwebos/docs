---
title: 使用 Azure 开发测试实验室的常见方案
description: 本文提供使用 Azure 开发测试实验室的主要方案，以及开始在组织中使用该服务的两个常规路径。
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481589"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure 开发测试实验室的常见方案
根据企业的需要，可以配置开发测试实验室来满足不同的要求。  本文介绍常见的方案。 每个方案都涵盖了使用开发测试实验室和资源来实现这些方案所带来的好处。  

- 开发人员桌面
- 测试环境
- 培训会话、动手实验和编程马拉松
- 沙盒调查
- 教室实验室

## <a name="developer-desktops"></a>开发人员桌面
开发人员针对不同的项目对开发计算机有不同的要求。 借助开发测试实验室，开发人员可以访问按需虚拟机，这些虚拟机配置为适合其最常见的方案。 开发测试实验室具有以下优点：

- 组织可以提供公共开发计算机，确保团队之间的一致性。
- 开发人员可以根据需要快速设置其开发计算机，也可以 [声明现有的预先配置的计算机](devtest-lab-add-claimable-vm.md)。
- 开发人员无需订阅级权限即可以自助服务方式预配资源。
- IT 或管理员可以 [预定义网络拓扑](devtest-lab-configure-vnet.md) ，开发人员无需任何特殊访问权限即可以简单直观的方式直接使用网络拓扑。
- 开发人员可以根据需要轻松地 [自定义](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 开发计算机。
- 管理员可以控制成本，只需确保：
    - 开发人员无法获取超过开发所需的[更多 vm](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)
    - [Vm](devtest-lab-set-lab-policy.md#set-auto-shutdown) 在不使用时关闭
    - 仅允许特定实验室的[VM 实例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每个实验室的[成本目标和通知](devtest-lab-configure-cost-management.md)。

有关进一步阅读，请参阅 [将 Azure 开发测试实验室用于开发人员](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>测试环境
在整个企业中创建和管理测试环境可能需要做大量工作。 借助开发测试实验室，可轻松创建、更新或复制测试环境。 它允许团队在需要时访问完全配置的环境。 在此方案中，开发测试实验室具有以下优势：

- 组织可以提供常见的测试环境，确保团队之间的一致性。
- 测试人员可以通过使用可重用模板快速设置 Windows 和 Linux 环境来测试其应用程序的最新版本。
- 管理员可以将实验室连接到 Azure DevOps 以启用 DevOps 方案
- 实验室所有者可以通过确保以下各内容来控制成本：
    - [环境中的 vm](devtest-lab-set-lab-policy.md#set-auto-shutdown) 在不使用时关闭
    - 仅允许特定实验室的[VM 实例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每个实验室的[成本目标和通知](devtest-lab-configure-cost-management.md)。

有关详细信息，请参阅 [将 Azure 开发测试实验室用于 VM 和 PaaS 测试环境](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙盒调查
开发人员经常调查不同的技术或基础结构设计。 默认情况下，在其自己的资源组中创建使用开发测试实验室创建的所有环境。 开发测试实验室用户仅获取对这些资源的读取访问权限。 但是，对于需要更多控制的开发人员，可以更新实验室范围的设置，以便为其创建的每个环境的发起开发测试实验室用户提供 [参与者权限](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) 。  借助开发测试实验室，可向开发人员提供对在实验室中创建的环境的参与者权限。  此方案允许开发人员添加和/或更改其开发或测试环境所需的 Azure 资源。 " [按资源收费](devtest-lab-configure-cost-management.md#view-cost-by-resource) " 页允许实验室所有者跟踪用于调查的每个环境的成本。

有关详细信息，请参阅 [设置对环境资源组的访问权限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>培训、动手实验和编程马拉松 
Azure 开发测试实验室中的实验室充当暂时性活动（如讨论会、动手实验、培训或编程马拉松）的绝佳容器。  此服务允许你创建实验室，可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 在此方案中，开发测试实验室具有以下优势：

- [策略](devtest-lab-set-lab-policy.md) 确保学员只获取所需资源的数量，如虚拟机。
- 预配置和创建的计算机是通过学员中的单个操作来 [声明](devtest-lab-add-claimable-vm.md) 的。
- 实验室通过访问 [实验室的 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)与学员共享。
- 虚拟机上的[过期日期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)可确保在不再需要时删除计算机。
- 当定型结束时，很容易 [删除实验室](devtest-lab-delete-lab-vm.md#delete-a-lab) 和所有 [相关资源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) 。

有关详细信息，请参阅 [使用 Azure 开发测试实验室进行培训](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念证明与缩放部署
一旦您决定浏览开发测试实验室，就会有两个常规路径：概念证明与缩放部署。  

**规模化部署**包括数周/数月的审查和规划，其目的是将开发测试实验室部署到具有数百或数千个开发人员的整个企业。

**概念证明**部署侧重于从单个团队中集中精力建立组织价值。 虽然想想规模化部署比较诱人，但是该方法通常比概念验证选项更容易失败。 因此，我们建议从小型部署开始，从第一个团队那里学习经验，对两到三个另外的团队重复相同的方法，然后根据所获得的知识规划规模化部署。 为实现成功的概念验证，我们建议你挑选一两个团队，识别其方案（开发环境与测试环境），记录其当前的使用案例，并部署开发测试实验室。

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [开发测试实验室概念](devtest-lab-concepts.md)
- [开发测试实验室常见问题解答](devtest-lab-faq.md)

