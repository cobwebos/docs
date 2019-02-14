---
title: 协调 Azure 开发测试实验室的实施
description: 本文提供有关在组织中协调 Azure 开发测试实验室的实施的指导。
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
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242985"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>协调 Azure 开发测试实验室的实施
本文提供快速部署和实施 Azure 开发测试实验室的建议方法。 下图突出显示了整个过程（规范性指导），同时反映了支持各种行业要求和方案的灵活性。

![实施 Azure 开发测试实验室的步骤](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>假设
本文假设在实施开发测试实验室试验之前已准备好以下各项：

- **Azure 订阅**：试点团队有权在 Azure 订阅中部署资源。 如果工作负荷仅仅是开发和测试，则我们建议选择企业开发测试套餐，以获得更多可用映像并降低 Windows 虚拟机的费用。
- **本地访问**：已根据需要配置本地访问。 可以通过站点到站点 VPN 连接或 Express Route 实现本地访问。 通过需要花费几周时间才能建立 Express Route 连接，因此，我们建议在启动项目之前事先部署好 Express Route。
- **试点团队**：确定了使用开发测试实验室的初始开发项目团队以及适用的开发或测试活动，并为这些团队建立了要求/目标。

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>里程碑 1：建立初始网络拓扑和设计
部署 Azure 开发测试实验室解决方案时，第一个侧重点是为虚拟机建立计划的连接。 以下步骤概述了所需的过程：

1. 定义分配到 Azure 中开发测试实验室订阅的**初始 IP 地址范围**。 此步骤需要预测 VM 的预期使用数量，以便能够为将来的扩展提供足够大的区块。
2. 确定开发测试实验室的**所需访问方法**（例如外部/内部访问）。 此步骤的一个要点是确定虚拟机是否有公共 IP 地址（即，是否可从 Internet 直接访问）。
3. 确定并规定其他 Azure 云环境和本地的**连接方法**。 如果启用了通过 Express Route 进行强制路由，则虚拟机可能需要相应的代理配置才能遍历企业防火墙。
4. 如果 VM 要**加入域**，请确定它们是要加入基于云的域（例如 AAD 目录服务）还是本地域。 如果加入本地域，请确定虚拟机要加入 Active Directory 中的哪个组织单位 (OU)。 此外，确认用户是否有权执行加入（或者建立一个可在域中创建计算机记录的服务帐户）

## <a name="milestone-2-deploy-the-pilot-lab"></a>里程碑 2：部署试点实验室
准备好网络拓扑后，可执行以下步骤来创建第一个/试点实验室：

1. 创建初始开发测试实验室环境（[此处](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md)提供了分步说明）
2. 确定允许在实验室中使用的 VM 映像和大小。 确定是否可将自定义映像上传到 Azure，以便在开发测试实验室中使用。
3. 通过为实验室（实验室所有者和实验室用户）创建初始的基于角色的访问控制 (RBAC) 来保护对实验室的访问。 我们建议对开发测试实验室的标识结合使用同步的 Active Directory 帐户和 Azure Active Directory。
4. 将开发测试实验室配置为使用计划、成本管理、可认领 VM、自定义映像或公式等策略。
5. 建立 Azure Repos/Git 等联机存储库。
6. 确定是要使用公共存储库、专用存储库，还是两者的组合。 组织用于部署和长期保留的 JSON 模板。
7. 根据需要创建自定义项目。 此步骤是可选的。 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>里程碑 3：文档、支持、学习和改进
初始试点团队可能需要深入的支持才能入门。 他们可以凭经验来确保提供正确的文档和支持，以便持续实施 Azure 开发测试实验室。

1. 向试点团队介绍新的开发测试实验室资源（演示、文档）
2. 基于试点团队的经验，根据需要规划和交付文档
3. 将新团队入职过程（创建和配置实验室中、提供访问权限，等等）正式化
4. 根据最初的领会，验证 IP 地址空间的原始预测是否仍然合理且准确
5. 确保已完成相应的合规性与安全性评审

## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[Azure 开发测试实验室基础设施治理](devtest-lab-guidance-governance-resources.md)
