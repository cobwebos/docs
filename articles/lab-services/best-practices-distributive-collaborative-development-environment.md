---
title: Azure 开发测试实验室资源的分布式协作开发
description: 提供设置分布式开发环境以开发开发测试实验室资源的最佳实践。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170123"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure 开发测试实验室资源的分布式开发和协作开发的最佳实践
分布式协作开发允许不同的团队或人员开发和维护基本代码。 若要成功，开发过程取决于创建、共享和集成信息的能力。 此密钥开发原则可在 Azure 开发测试实验室中使用。 实验室中有多种类型的资源，这些资源通常分布于企业中的不同实验室。 不同类型的资源集中在两个方面：

- 内部存储在实验室内的资源（基于实验室）
- 存储在[连接到实验室的外部存储库中的](devtest-lab-add-artifact-repo.md)资源（基于代码存储库）。 

本文档介绍了一些最佳做法，它们允许跨多个团队进行协作和分发，同时确保所有级别的自定义和质量。

## <a name="lab-based-resources"></a>基于实验室的资源

### <a name="custom-virtual-machine-images"></a>自定义虚拟机映像
你可以将自定义映像的常见源部署到实验室。 有关详细信息，请参阅[映像工厂](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)特定于实验室，没有分布机制。 实验室成员执行公式的所有开发工作。 

## <a name="code-repository-based-resources"></a>基于代码存储库的资源
有两种不同的功能，它们基于代码存储库、项目和环境。 本文将介绍这些功能，以及如何最有效地设置存储库和工作流，以允许在组织级别或团队级别自定义可用项目和环境。  此工作流基于标准[源代码控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)。 

### <a name="key-concepts"></a>关键概念
项目的源信息包括元数据和脚本。 环境的源信息包括具有任何支持文件（例如 PowerShell 脚本、DSC 脚本、Zip 文件等）的元数据和资源管理器模板。  

### <a name="repository-structure"></a>存储库结构  
源代码管理（SCC）的最常见配置是设置一个多层结构，用于存储实验室中使用的代码文件（资源管理器模板、元数据和脚本）。 具体而言，请创建不同的存储库来存储不同级别的业务所管理的资源：   

- 公司范围内的资源。
- 业务单元/部门范围资源
- 团队特定资源。

其中每个级别都链接到不同的存储库，其中，主分支需要具有生产质量。 每个存储库中的[分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)将用于开发这些特定资源（项目或模板）。 此结构与开发测试实验室非常一致，因为可以轻松地将多个存储库和多个分支同时连接到组织的实验室。 存储库名称包含在用户界面（UI）中，以便在具有相同的名称、说明和发布者时避免混淆。
     
下图显示了两个存储库：由 IT 部门维护的公司存储库，以及 R & D 部门维护的部门存储库。

![分布式开发环境示例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
这种分层结构允许开发，同时在主分支中保持较高的质量级别，同时将多个存储库连接到实验室，从而实现更大的灵活性。

## <a name="next-steps"></a>后续步骤    
请参阅以下文章：

- 使用[Azure 门户](devtest-lab-add-artifact-repo.md)或通过[Azure 资源管理模板](add-artifact-repository.md)将存储库添加到实验室
- [开发测试实验室项目](devtest-lab-artifact-author.md)
- [开发测试实验室环境](devtest-lab-create-environment-from-arm.md)。
