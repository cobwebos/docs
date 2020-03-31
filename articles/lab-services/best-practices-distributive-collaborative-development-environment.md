---
title: Azure 开发人员测试实验室资源的分布式协作开发
description: 提供用于设置分布式和协作开发环境以开发 DevTest Labs 资源的最佳做法。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170123"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure 开发人员测试实验室资源的分布式和协作开发最佳实践
分布式协作开发允许不同的团队或人员开发和维护代码库。 要取得成功，开发过程取决于创建、共享和集成信息的能力。 此关键开发原则可在 Azure 开发人员测试实验室中使用。 实验室中有几种类型的资源通常分布在企业内的不同实验室之间。 不同类型的资源集中在两个领域：

- 内部存储在实验室内的资源（基于实验室）
- 存储在[连接到实验室的外部存储库](devtest-lab-add-artifact-repo.md)中的资源（基于代码存储库）。 

本文档介绍了一些最佳实践，这些最佳实践允许跨多个团队进行协作和分发，同时确保所有级别的自定义和质量。

## <a name="lab-based-resources"></a>基于实验室的资源

### <a name="custom-virtual-machine-images"></a>自定义虚拟机映像
您可以拥有每晚部署到实验室的自定义映像的常见源。 有关详细信息，请参阅[映像工厂](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)特定于实验室，没有分布机制。 实验成员做公式的所有开发。 

## <a name="code-repository-based-resources"></a>基于代码存储库的资源
有两个不同的功能基于代码存储库、工件和环境。 本文介绍了这些功能以及如何最有效地设置存储库和工作流，以便能够在组织级别或团队级别自定义可用的工件和环境。  此工作流基于标准[源代码控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)。 

### <a name="key-concepts"></a>关键概念
工件的源信息包括元数据和脚本。 环境的源信息包括元数据和资源管理器模板，其中包含任何支持文件，如 PowerShell 脚本、DSC 脚本、Zip 文件等。  

### <a name="repository-structure"></a>存储库结构  
源代码管理 （SCC） 的最常见配置是设置多层结构，用于存储实验室中使用的代码文件（资源管理器模板、元数据和脚本）。 具体而言，创建不同的存储库来存储由不同业务级别管理的资源：   

- 全公司资源。
- 业务单位/全部门资源
- 特定于团队的资源。

每个级别链接到不同的存储库，其中主分支需要具有生产质量。 每个存储库中的[分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)将用于开发这些特定资源（工件或模板）。 此结构与 DevTest Labs 很好地一致，因为您可以轻松地同时将多个存储库和多个分支连接到组织的实验室。 存储库名称包含在用户界面 （UI） 中，以避免在名称、说明和发布者相同时出现混淆。
     
下图显示了两个存储库：由 IT 部门维护的公司存储库和由 R&D 部门维护的部门存储库。

![分配和协作开发环境示例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
这种分层结构允许开发，同时在主分支保持更高级别的质量，同时将多个存储库连接到实验室，从而获得更大的灵活性。

## <a name="next-steps"></a>后续步骤    
请参阅以下文章：

- 使用[Azure 门户](devtest-lab-add-artifact-repo.md)或通过[Azure 资源管理模板](add-artifact-repository.md)将存储库添加到实验室
- [开发人员测试实验室项目](devtest-lab-artifact-author.md)
- [开发人员测试实验室环境](devtest-lab-create-environment-from-arm.md)。
