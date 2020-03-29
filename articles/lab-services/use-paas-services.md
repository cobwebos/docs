---
title: 在 Azure 开发人员测试实验室中使用平台即服务 （PaaS） 服务
description: 了解如何在 Azure 开发人员测试实验室中使用平台即服务 （通过） 服务。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169194"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中使用平台即服务 （PaaS） 服务
开发人员测试实验室通过环境功能支持 PaaS。 开发人员测试实验室中的环境由 Git 存储库中预先配置的 Azure 资源管理器模板提供支持。 环境可以同时包含 PaaS 和 IaaS 资源。 它们允许您创建复杂的系统，这些系统可以包括 Azure 资源，如虚拟机、数据库、虚拟网络和 Web 应用，这些资源被自定义以协同工作。 这些模板允许使用源代码控制进行一致的部署和改进环境管理。 

正确设置的系统允许以下方案： 

- 具有独立和多种环境的开发人员
- 异步对不同配置进行测试
- 集成到暂存和生产管道中，无需任何模板更改
- 在同一实验室中同时使用开发机器和环境可简化管理和成本报告。  

DevTest Labs 资源提供程序代表实验室用户创建资源，因此无需向实验室用户授予额外的权限即可启用 PaaS 资源。 下图将服务结构群集作为实验室中的环境显示。

![将服务结构群集作为环境](./media/create-environment-service-fabric-cluster/cluster-created.png)

有关设置环境的详细信息，请参阅使用 Azure[资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 DevTest Labs 具有 Azure 资源管理器模板的公共存储库，可用于创建环境，而无需自己连接到外部 GitHub 源。 有关公共环境的详细信息，请参阅在[Azure 开发人员测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。

在大型组织中，开发团队通常提供自定义/隔离测试环境等环境。 业务单位或部门内的所有团队都可以使用一些环境。 IT 组可能希望提供其环境，以便组织中的所有团队都可以使用这些环境。  

## <a name="customizations"></a>自定义

#### <a name="sandbox"></a>沙盒 
实验室所有者可以自定义实验室环境，以将用户的角色从**读取器**更改为资源组中**的参与者**。 此功能位于实验室**的"配置和策略**"下的 **"实验室设置**"页中。 角色的此更改允许用户添加或删除该环境中的资源。 如果要进一步限制访问，请使用 Azure 策略。 此功能允许您自定义资源或配置，而无需在订阅级别访问。

#### <a name="custom-tokens"></a>自定义令牌
有一些自定义实验室信息位于资源组之外，特定于模板可以访问的环境。 以下是其中的一些方式： 

- 实验室网络识别
- 位置
- 存储存储资源管理器模板文件的存储帐户。 
 
#### <a name="lab-virtual-network"></a>实验室虚拟网络
将[环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)文章介绍如何修改资源管理器模板以使用令牌`$(LabSubnetId)`。 创建环境时，`$(LabSubnetId)`令牌将被第一个子网标记替换，其中**在虚拟机创建选项中使用**设置为**true**。 它允许我们的环境使用以前创建的网络。 如果要在测试环境中使用与暂存和生产相同的资源管理器模板，请使用`$(LabSubnetId)`资源管理器模板参数中的默认值。 

#### <a name="environment-storage-account"></a>环境存储帐户
DevTest 实验室支持使用[嵌套资源管理器模板](../azure-resource-manager/templates/linked-templates.md)。 [[部署嵌套 Azure 资源管理器模板用于测试环境》](deploy-nested-template-environments.md)一文解释了如何使用`_artifactsLocation`和`_artifactsLocationSasToken`令牌在主模板的嵌套文件夹中或主模板的嵌套文件夹中创建资源管理器模板的 URI。 有关这两个令牌的详细信息，请参阅 Azure 资源管理器的部署**项目**部分[- 最佳实践指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)。

## <a name="user-experience"></a>用户体验

## <a name="developer"></a>开发人员
开发人员使用相同的工作流来创建 VM 来创建特定环境。 他们选择环境与机器映像，并输入模板所需的必要信息。 每个具有环境的开发人员都允许部署更改和改进的内部循环调试。 可以随时使用最新的模板创建环境。  此功能允许销毁和重新创建环境，以帮助减少手动创建系统或从故障测试恢复的停机时间。  

### <a name="testing"></a>正在测试
DevTest Labs 环境允许异步独立测试特定代码和配置。 常见做法是使用单个拉取请求中的代码设置环境，并开始任何自动测试。 一旦自动测试运行到完成，任何手动测试都可以针对特定环境执行。 通常此过程作为 CI/CD 管道的一部分完成。 

## <a name="management-experience"></a>管理经验

### <a name="cost-tracking"></a>成本跟踪
成本跟踪功能包括不同环境中的 Azure 资源，作为总体成本趋势的一部分。 按资源进行的成本不会分解环境中的不同资源，而是将环境显示为单个成本。

### <a name="security"></a>安全性
使用 DevTest 实验室正确配置的 Azure 订阅[只能通过实验室限制对 Azure 资源的访问](devtest-lab-add-devtest-user.md)。 使用环境，实验室所有者可以允许用户使用批准的配置访问 PaaS 资源，而无需访问任何其他 Azure 资源。 在实验室用户自定义环境的方案中，实验室所有者可以允许参与者访问。 参与者访问权限允许实验室用户仅在托管资源组中添加或删除 Azure 资源。 它允许更轻松地跟踪和管理，而不是允许用户参与者访问订阅。

### <a name="automation"></a>自动化
自动化是大规模、有效生态系统的关键组成部分。 自动化对于跨订阅和实验室管理或跟踪多个环境是必需的。

### <a name="cicd-pipeline"></a>CI/CD 管道
DevTest Labs 中的 PaaS 服务可通过在实验室控制访问的集中部署中帮助改进 CI/CD 管道。

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- 
- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 开发人员测试实验室中使用自包含的服务结构群集创建环境](create-environment-service-fabric-cluster.md)
- [在 Azure 开发人员测试实验室中将环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)
- [将环境集成到 Azure DevOps CI/CD 管道中](integrate-environments-devops-pipeline.md)
 





