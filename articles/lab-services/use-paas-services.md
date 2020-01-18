---
title: 在 Azure 开发测试实验室中使用平台即服务（PaaS）服务
description: 了解如何在 Azure 开发测试实验室中使用平台即服务（Pass）服务。
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169194"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中使用平台即服务（PaaS）服务
PaaS 通过环境功能在开发测试实验室中受支持。 开发测试实验室中的环境通过 Git 存储库中的预配置 Azure 资源管理器模板来支持。 环境可以同时包含 PaaS 和 IaaS 资源。 它们允许你创建可包含 Azure 资源（例如虚拟机、数据库、虚拟网络和 Web 应用）的复杂系统，这些资源可自定义以协同工作。 这些模板允许使用源代码管理一致地部署和改善环境。 

正确设置的系统允许以下方案： 

- 具有独立环境和多个环境的开发人员
- 以异步方式测试不同的配置
- 集成到过渡和生产管道，无需任何模板更改
- 在同一实验室中同时拥有开发计算机和环境可以提高管理和成本报告的简易性。  

开发测试实验室资源提供程序代表实验室用户创建资源，因此，无需向实验室用户提供额外的权限即可使用 PaaS 资源。 下图显示了作为实验室环境的 Service Fabric 群集。

![将群集作为环境 Service Fabric](./media/create-environment-service-fabric-cluster/cluster-created.png)

有关设置环境的详细信息，请参阅[通过 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 开发测试实验室提供 Azure 资源管理器模板的公共存储库，你可以使用这些模板来创建环境，而无需自行连接到外部 GitHub 源。 有关公共环境的详细信息，请参阅[在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。

在大型组织中，开发团队通常会提供一些环境，如自定义/隔离测试环境。 业务部门或部门内的所有团队可能会使用环境。 IT 小组可能需要提供组织中的所有团队均可使用的环境。  

## <a name="customizations"></a>自定义

#### <a name="sandbox"></a>沙盒 
实验室所有者可以自定义实验室环境，以便将用户的角色从**读取**者更改为资源组中的**参与者**。 此功能位于实验室的 "**配置和策略**" 下的 "**实验室设置**" 页中。 此角色更改允许用户在该环境中添加或删除资源。 如果希望进一步限制访问权限，请使用 Azure 策略。 此功能可让你在订阅级别无需访问权限的情况下自定义资源或配置。

#### <a name="custom-tokens"></a>自定义令牌
资源组外部有一些自定义实验室信息，该信息特定于模板可以访问的环境。 以下是其中的一些方式： 

- 实验室网络标识
- 位置
- 存储资源管理器模板文件的存储帐户。 
 
#### <a name="lab-virtual-network"></a>实验室虚拟网络
将[环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)一文介绍了如何修改资源管理器模板以使用 `$(LabSubnetId)` 令牌。 创建环境后，会将 `$(LabSubnetId)` 标记替换为在**虚拟机创建选项中使用**的第一个子网标记设置为**true**。 它使我们的环境可以使用以前创建的网络。 如果要在作为过渡和生产的测试环境中使用相同资源管理器模板，请在资源管理器模板参数中使用 `$(LabSubnetId)` 作为默认值。 

#### <a name="environment-storage-account"></a>环境存储帐户
开发测试实验室支持使用[嵌套资源管理器模板](../azure-resource-manager/templates/linked-templates.md)。 [[部署用于测试环境的嵌套 Azure 资源管理器模板](deploy-nested-template-environments.md)一文介绍了如何使用 `_artifactsLocation` 和 `_artifactsLocationSasToken` 标记在主模板的嵌套文件夹中创建资源管理器模板的 URI。 有关这两个令牌的详细信息，请参阅 Azure 资源管理器的**部署项目**部分[-最佳实践指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)。

## <a name="user-experience"></a>用户体验

## <a name="developer"></a>Developer
开发人员使用相同的工作流创建 VM 来创建特定环境。 它们选择环境与计算机映像，并输入模板所需的必要信息。 拥有环境的每个开发人员都可以部署更改和改进的内部循环调试。 使用最新模板随时可以创建环境。  利用此功能，可以销毁并重新创建环境，以帮助减少因手动创建系统或从错误测试中恢复而导致的停机时间。  

### <a name="testing"></a>测试
开发测试实验室环境允许以异步方式独立测试特定的代码和配置。 常见的做法是在环境中设置来自单个拉取请求的代码，并启动任何自动测试。 自动测试完成运行后，可以针对特定环境执行任何手动测试。 此过程通常作为 CI/CD 管道的一部分完成。 

## <a name="management-experience"></a>管理体验

### <a name="cost-tracking"></a>成本跟踪
成本跟踪功能包含不同环境中的 Azure 资源，作为总体成本趋势的一部分。 成本按资源不会分解环境中的不同资源，而是将环境显示为单个成本。

### <a name="security"></a>安全性
使用开发测试实验室正确配置的 Azure 订阅可以[仅通过实验室限制对 azure 资源的访问](devtest-lab-add-devtest-user.md)。 利用环境，实验室所有者可允许用户使用批准的配置访问 PaaS 资源，而无需访问任何其他 Azure 资源。 在实验室用户自定义环境的方案中，实验室所有者可以允许参与者访问。 参与者访问权限允许实验室用户仅在托管资源组中添加或删除 Azure 资源。 它允许更轻松地进行跟踪和管理，而不允许用户参与者访问订阅。

### <a name="automation"></a>自动化
自动化是大规模、有效生态系统的关键组件。 自动化是处理跨订阅和实验室管理或跟踪多个环境所必需的。

### <a name="cicd-pipeline"></a>CI/CD 管道
开发测试实验室中的 PaaS 服务可以通过在实验室控制访问权限的集中部署来帮助改善 CI/CD 管道。

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- 
- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
- [使用 Azure 开发测试实验室中的独立 Service Fabric 群集创建环境](create-environment-service-fabric-cluster.md)
- [在 Azure 开发测试实验室中将环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)
- [将环境集成到 Azure DevOps CI/CD 管道](integrate-environments-devops-pipeline.md)
 





