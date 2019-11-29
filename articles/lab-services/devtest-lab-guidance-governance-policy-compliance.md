---
title: Azure 开发测试实验室中的公司策略和符合性
description: 本文提供有关监管 Azure 开发测试实验室基础结构的公司策略与合规性的指导。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560505"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Azure 开发测试实验室基础结构的监管 - 公司策略与合规性
本文提供有关监管 Azure 开发测试实验室基础结构的公司策略与合规性的指导。 

## <a name="public-vs-private-artifact-repository"></a>公共与专用项目存储库

### <a name="question"></a>问题
组织何时应在开发测试实验室中使用公共项目存储库或专用项目存储库？

### <a name="answer"></a>答案
[公共项目存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)提供初始的一组最常用软件包。 它可帮助进行快速部署，而无需花费时间来重现常见开发人员工具和外接程序。你可以选择部署其自己的专用存储库。 可以同时使用公共和专用存储库。 还可以选择禁用公共存储库。 部署专用存储库的条件应根据以下问题和注意事项而定：

- 组织是否必须在其开发测试实验室套餐中提供企业许可的软件？ 如果是，则应创建专用存储库。
- 组织是否开发可提供特定操作的、在总体预配过程中所需的自定义软件？ 如果是，则应部署专用存储库。
- 如果组织的监管策略需要隔离，并且外部存储库不在组织的直接配置管理范畴内，则应部署专用项目存储库。 在此过程中，可以复制公共存储库的初始副本，并将其与专用存储库集成。 然后可以禁用公共存储库，使组织中的任何人都不再可以访问它。 此方法会强制组织中的所有用户只能使用组织批准的单个存储库，并可以最大程度地减少配置偏差。

### <a name="single-repository-or-multiple-repositories"></a>单个存储库或多个存储库 

### <a name="question"></a>问题
组织是要规划单个存储库还是允许多个存储库？

### <a name="answer"></a>答案
作为组织总体监管和配置管理策略的一部分，我们建议使用集中式存储库。 使用多个存储库时，它们可能会逐渐变成孤立的非托管软件。 使用中心存储库时，多个团队可对其项目使用此存储库中的项目。 此方法强制实施标准化、安全性和易管理性方面的措施，并消除了重复工作。 在集中化过程中，建议采取以下措施来实现长期管理和可持续性：

- 将 Azure Repos 与 Azure 订阅用于身份验证和授权的同一 Azure Active Directory 租户相关联。
- 在 Azure Active Directory 中创建名为“所有开发测试实验室开发人员”的集中管理的组。 应将参与项目开发的任何开发人员置于此组中。
- 可使用同一个 Azure Active Directory 组来提供对 Azure Repos 存储库和实验室的访问。
- 在 Azure Repos 中，应使用分支或分叉将开发中存储库与主要生产存储库相隔离。 在正确完成代码评审后，使用拉取请求将内容仅添加到主分支。 代码评审员批准更改后，负责主分支维护工作的开发人员主管合并更新的代码。 

## <a name="corporate-security-policies"></a>企业安全策略

### <a name="question"></a>问题
组织如何确保企业安全策略部署到位？

### <a name="answer"></a>答案
组织可通过以下措施实现此目的：

1. 制定并发布全面的安全策略。 该策略阐述了软件和云资产的可接受用法相关规则。 它还规定了哪些行为肯定会违反策略。 
2. 开发自定义映像、自定义项目和部署过程，以便在使用 Active Directory 定义的安全领域内进行协调。 此方法会强制实施企业边界，并规定一套通用的环境控制措施。 开发人员在整个过程中开发和遵循安全开发生命周期时，可以考虑这些针对环境的控制措施。 目标也是使提供的环境不会过度施加限制，从而对开发带来阻碍，而是实施一套合理的控制措施。 包含实验室虚拟机的组织单位 (OU) 的组策略可以是生产环境中的所有组策略的子集。 或者，它们可以是一个用于适当缓解任何所识别到的风险的补充集。

## <a name="data-integrity"></a>数据完整性

### <a name="question"></a>问题
组织如何确保数据完整性，以防止远程开发人员删除代码或者引入恶意软件或未经批准的软件？

### <a name="answer"></a>答案
可通过多个控制层来缓解在开发测试实验室中进行协作的外部远程顾问、合同工或员工带来的威胁。 

如前所述，第一步是必须草拟并制定可接受的使用策略，其中明确阐述了违反策略所造成的后果。 

第一个远程访问控制层是通过不与实验室直接连接的 VPN 来应用远程访问策略。 

第二个控制层是应用组策略对象集，以防止通过远程桌面进行复制粘贴。 可以实施一个网络策略来禁止环境中的出站服务，例如环境外部的 FTP 和 RDP 服务。 用户定义的路由可以强制将所有 Azure 网络流量发回到本地，但除非通过可以扫描内容和会话的代理进行控制，否则该路由无法考虑到可能允许上传数据的所有 URL。 在支持开发测试实验室的虚拟网络中，公共 IP 可能限制为禁止桥接外部网络资源。

最终，需要在整个组织中应用相同类型的限制，因此，还必须考虑到可移动媒体或者可接受发布内容的外部 URL 使用的所有可能方法。 评审和实施安全策略时，请咨询安全专家。 有关更多建议，请参阅 [Microsoft 网络安全性](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)。


## <a name="next-steps"></a>后续步骤
参阅[应用程序迁移和集成](devtest-lab-guidance-governance-application-migration-integration.md)。
