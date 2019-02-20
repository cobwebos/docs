---
title: Azure 开发测试实验室基础设施治理
description: 本文提供有关监管 Azure 开发测试实验室基础结构的指导。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: aff57e58ffd247bcc697908f2f25cbf17ff9b91a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244124"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Azure 开发测试实验室基础结构的监管 - 资源
本文介绍组织中开发测试实验室的资源调配和管理。 

## <a name="align-within-an-azure-subscription"></a>Azure 订阅中的调配 

### <a name="question"></a>问题
如何调配 Azure 订阅中的开发测试实验室资源？

### <a name="answer"></a>Answer
在组织开始使用 Azure 进行一般性的应用程序开发之前，IT 规划人员首先应该评审如何在整个服务产品组合中引入功能。 评审的各个方面应可解决以下问题：

- 如何衡量与应用程序开发生命周期关联的成本？
- 组织如何使提议的服务产品与企业安全策略相符？ 
- 是否需要通过分段来隔离开发环境和生产环境？ 
- 针对长远的轻松管理、稳定性和发展引入了哪些控制措施？

**第一项建议的做法**是评审组织中对生产订阅和开发订阅做了划分的 Azure 分类。 在下图中，建议的分类能够对开发/测试和生产环境进行逻辑分离。 使用此方法，组织可以引入计费代码来单独跟踪与每个环境关联的成本。 有关详细信息，请参阅[出于合规目的监管订阅](/azure/architecture/cloud-adoption/appendix/azure-scaffold)。 此外，可以使用 [Azure 标记](../azure-resource-manager/resource-group-using-tags.md)来组织资源，以便于跟踪和计费。

**第二项建议的做法**是在 Azure 企业门户中启用开发测试订阅。 这样，组织便可以运行 Azure 企业订阅中通常不会提供的客户端操作系统。 然后，使用仅支付计算费用的企业软件，而无需担心许可问题。 这可以确保只需根据消耗量支付指定服务的费用，包括 IaaS 中的库映像，例如 Microsoft SQL Server。 企业协议 (EA) 客户和即用即付客户可以分别在[此处](https://azure.microsoft.com/offers/ms-azr-0148p/)和[此处](https://azure.microsoft.com/offers/ms-azr-0023p/)找到有关 Azure 开发测试订阅的详细信息。

![资源与订阅相符](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

此模型可让组织灵活地大规模部署 Azure 开发测试实验室。 组织可以支持各个业务单位运作的数百个实验室，其中可以并行运行 100 到 1000 个虚拟机。 这样可以提升集中式企业实验室解决方案的概念，并可以共享相同的配置管理和安全控制原则。

此模型还确保组织不会耗尽其 Azure 订阅关联的资源限制。 有关订阅和服务限制的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。 开发测试实验室预配过程可能会消耗大量的资源组。 可以通过 Azure 开发测试订阅中的支持请求来请求提高限制。 随着开发订阅的用量增加，生产订阅中的资源不受影响。 有关缩放开发测试实验室的详细信息，请参阅[开发测试实验室中的缩放配额和限制](devtest-lab-scale-lab.md)。

需要考虑的常见订阅级限制是如何分配网络 IP 范围，以支持生产和开发订阅。 这些分配应该考虑到不同时间段的发展（假设本地连接或其他网络拓扑需要企业管理其网络堆栈，而不是默认使用 Azure 的实施方案）。 建议的做法是在一些虚拟网络中分配较大的 IP 地址前缀，并将这些虚拟网络分割成多个大型子网，而不是组建多个包含小型子网的虚拟网络。 例如，使用 10 个订阅时，可以定义 10 个虚拟网络（每个订阅对应一个虚拟网络）。 不需要隔离的所有实验室可以共享订阅 VNet 中的同一子网。

## <a name="maintain-naming-conventions"></a>保留命名约定

### <a name="question"></a>问题
如何在整个开发测试实验室环境中保留命名约定？

### <a name="answer"></a>Answer
你可能需要将当前企业命名约定扩展到 Azure 运营，并在整个开发测试实验室环境中保持这种命名约定的一致性。

部署开发测试实验室时，我们建议使用特定的起始策略。 通过中心脚本和 JSON 模板部署这些策略，以实施一致性。 可以通过在订阅级别应用的 Azure 策略来实施命名策略。 有关 Azure Policy 的 JSON 示例，请参阅 [Azure Policy 示例](../azure-policy/json-samples.md)。

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>每个实验室的用户数和每个组织的实验室数

### <a name="question"></a>问题 
如何确定每个实验室的用户数与整个组织中所需实验室总数的比？

### <a name="answer"></a>Answer
我们建议将同一开发项目关联的业务单位和开发组关联到同一实验室。 这样，便可以对这两个组应用相同类型的策略、映像和关闭策略。 

可能还需要考虑地理边界。 例如，美国 (US) 东北部的开发人员可能会使用美国东部 2 区域中预配的实验室。 另外，位于德克萨斯州达拉斯市和科罗拉多州丹佛市的开发人员可能会定向为使用美国中南部的资源。 如果能够与外部第三方展开协作，则可为这些开发人员分配未由内部开发人员使用的实验室。 

还可以将实验室用于 Azure DevOps 项目中的特定项目。 然后，通过允许访问这两组资源的指定 Azure Active Directory 组应用安全性。 分配到该实验室的虚拟网络可以是用于整合用户的另一个边界。

## <a name="deletion-of-resources"></a>资源的删除

### <a name="question"></a>问题
如何防止删除实验室中的资源？

### <a name="answer"></a>Answer
我们建议在实验室级别设置适当的权限，以便只有经过授权的用户才能删除资源或更改实验室策略。 应将开发人员放置在“开发测试实验室用户”组中。 开发人员主管或基础结构主管应是“开发测试实验室所有者”。 我们建议仅指定两个实验室所有者。 此策略根据代码存储库的扩展而扩展，以避免损坏。 实验室用户有权使用资源，但不能更新实验室策略。 请参阅以下文章，其中列出了每个内置组在实验室中拥有的角色和权限：[在 Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="move-lab-to-another-resource-group"></a>将实验室移到另一个资源组 

### <a name="question"></a>问题
是否支持将实验室移到另一个资源组？

### <a name="answer"></a>Answer
是的。 从实验室的主页导航到“资源组”页。 然后，在工具栏中选择“移动”，并选择要移到其他资源组的实验室。 创建实验室时，会自动创建一个资源组。 但是，你可能需要将实验室移到遵循企业命名约定的其他资源组。 

## <a name="next-steps"></a>后续步骤
参阅[管理成本和所有权](devtest-lab-guidance-governance-cost-ownership.md)。
