---
title: Azure 开发测试实验室基础设施治理
description: 本文介绍 Azure 开发测试实验室基础设施治理。
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
ms.openlocfilehash: 7832691812d8f10342dc7df20a7cfab7265f2d9d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243512"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure 开发测试实验室基础设施治理 - 管理成本和所有权
打算构建开发和测试环境时，成本和所有权是主要的考量。 本部分介绍如何在环境中进行成本优化并落实所有权。

## <a name="optimize-for-cost"></a>成本优化

### <a name="question"></a>问题
如何在开发测试实验室环境中进行成本优化？

### <a name="answer"></a>Answer
开发测试实验室有许多内置的功能，用于进行成本优化。 请参阅[成本管理与阈值](devtest-lab-configure-cost-management.md)以及[策略](devtest-lab-set-lab-policy.md)这两篇文章，了解如何限制用户的活动。 

在将开发测试实验室用于开发和测试工作负荷时，可以考虑使用企业协议中的[企业开发/测试订阅权益](https://azure.microsoft.com/offers/ms-azr-0148p/)。 另外，如果是即用即付客户，可以考虑[即用即付开发测试套餐](https://azure.microsoft.com/offers/ms-azr-0023p/)。

此方法具有很多优点：

- Windows 虚拟机、云服务、HDInsight、应用服务和逻辑应用的开发/测试费率特别低
- 其他 Azure 服务的企业协议 (EA) 费率也很优惠
- 可以访问库中的专属开发/测试映像，包括 Windows 8.1 和 Windows 10
 
只有活动的 Visual Studio 订阅者（标准订阅、年度云订阅和月度云订阅）可使用企业开发/测试订阅中运行的 Azure 资源， 但最终用户也可访问该应用程序来提供反馈或执行验收测试。 在此订阅内，只能将资源用于开发和测试应用程序，且没有运行时间保证。

如果决定使用开发测试套餐，则请注意，此权益专用于开发和应用程序测试。 订阅内的使用不带有财务支持的 SLA，Azure DevOps 和 HockeyApp 的使用除外。

## <a name="define-a-role-based-access-across-your-organization"></a>定义基于角色的跨组织访问权限
### <a name="question"></a>问题
如何为我的开发测试实验室环境定义基于角色的访问控制才能确保在开发人员/测试人员完成其工作时由 IT 部门进行治理？ 

### <a name="answer"></a>Answer
可以使用一种广泛的模式，但具体细节取决于你的组织。

中心 IT 部门只应拥有必需的控制，让项目和应用程序团队拥有必需级别的控制。 通常情况下，这意味着中心 IT 部门拥有订阅并负责核心 IT 功能，例如网络配置。 订阅的**所有者**集应该小。 这些所有者可以在需要时指定其他所有者，或者应用订阅级别的策略，例如“禁止公共 IP”。

部分用户（例如第 1 层或第 2 层支持人员）可能需要在整个订阅中进行访问。 在这种情况下，建议为这些用户提供管理资源所需的“参与者”访问权限，但不提供用户访问权限，也不调整策略。

开发测试实验室资源应该由那些接近项目/应用程序团队的所有者拥有。 这是因为他们了解团队在计算机和必需软件方面的要求。 在大多数组织中，此开发测试实验室资源的所有者通常是项目/开发主管。 此所有者可以管理实验室环境中的用户和策略，可以管理开发测试实验室环境中的所有 VM。

应该将项目/应用程序团队成员添加到开发测试实验室用户角色中。 这些用户可以创建虚拟机（前提是遵循实验室和订阅级别的策略）。 他们也可以管理自己的虚拟机。 他们不能管理属于其他用户的虚拟机。

有关详细信息，请参阅 [Azure 企业基架 - 规范性订阅治理](/azure/architecture/cloud-adoption/appendix/azure-scaffold)文档。


## <a name="next-steps"></a>后续步骤
请参阅[企业策略和符合性](devtest-lab-guidance-governance-policy-compliance.md)。
