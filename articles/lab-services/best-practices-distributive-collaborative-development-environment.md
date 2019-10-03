---
title: 分布式 Azure 开发测试实验室资源的协作开发 |Microsoft Docs
description: 提供有关设置分布式和协作开发环境来开发开发测试实验室资源的最佳实践。
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543773"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure 开发测试实验室资源的分布式和协作开发的最佳做法
分布式协作开发，不同的团队或开发和维护基本代码的人。 若要成功，在开发过程取决于创建、 共享和将信息集成的功能。 在 Azure 开发测试实验室，可以使用此密钥的开发原则。 有几种类型的实验室中通常分布之间在企业中的不同实验室的资源。 不同类型的资源将焦点放到两个方面：

- 在内部存储实验室 （实验室基于） 中的资源
- 在存储资源[连接到实验室的外部存储库](devtest-lab-add-artifact-repo.md)（基于存储库的代码）。 

本文档介绍了一些最佳做法，以跨多个团队允许协作和分发，同时确保了自定义和所有级别的质量。

## <a name="lab-based-resources"></a>基于实验室的资源

### <a name="custom-virtual-machine-images"></a>自定义虚拟机映像
您可以部署到实验室在夜间的自定义映像的一个常见原因。 有关详细信息，请参阅[映像工厂](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)是特定于实验室的但没有分发机制。 实验室成员执行操作公式的所有的开发。 

## <a name="code-repository-based-resources"></a>代码存储库基于资源
有两个不同的功能根据代码存储库、 项目和环境。 这篇文章都要通过功能以及如何最有效地设置存储库和工作流以允许自定义的可用项目和环境在组织级别或团队级别的功能。  此工作流基于标准[源代码控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)。 

### <a name="key-concepts"></a>关键概念
项目的源信息包括元数据，脚本。 环境的源信息包括元数据和资源管理器模板与 PowerShell 脚本、 DSC 脚本、 Zip 文件等任何支持文件。  

### <a name="repository-structure"></a>存储库结构  
源代码管理 (SCC) 的最常见配置是设置在实验室中存储到使用的代码文件 （资源管理器模板、 元数据和脚本） 的多层结构。 具体而言，创建不同的存储库用于存储在管理的业务的不同级别的资源：   

- 公司范围内的资源。
- 业务单位/部门范围的资源
- 特定于团队的资源。

链接到不同的存储库，其中主分支必须投入实际生产运行的是两个级别。 [分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)在每个存储库中的目的是，这些特定资源 （项目或模板） 的开发。 你可以轻松连接多个存储库和多个分支在同一时间对组织的实验室时，此结构将开发测试实验室更好地与对齐。 存储库名称包含在用户界面 (UI) 以避免混淆，如果有相同的名称、 说明和发布服务器。
     
下图显示了两个存储库： 由 IT 部门，维护一个公司存储库和维护的研发部门的部门存储库。

![示例分布式和协作开发环境](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
此层次的结构可以同时在主分支保持较高的质量级别，而使连接到实验室的多个存储库可以在更大的灵活性的开发。

## <a name="next-steps"></a>后续步骤    
请参阅以下文章：

- 存储库中将添加到使用[Azure 门户](devtest-lab-add-artifact-repo.md)或通过[Azure 资源管理模板](add-artifact-repository.md)
- [开发测试实验室项目](devtest-lab-artifact-author.md)
- [开发测试实验室环境](devtest-lab-create-environment-from-arm.md)。
