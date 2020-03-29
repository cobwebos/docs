---
title: 集成 Azure 开发人员测试实验室和 DevOps |微软文档
description: 了解如何在企业环境中的连续集成 （CI）/连续交付 （CD） 管道中使用 Azure DevTest Labs 的实验室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078919"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>集成 Azure 开发人员测试实验室和 Azure 开发人员计划
DevOps 是一种软件开发方法，将软件开发 （Dev） 与系统的操作 （Ops） 集成在一起。 此系统可能会根据业务目标提供新功能、更新和修复。 此方法涵盖一切，包括根据目标、使用模式和客户反馈设计新功能;修复、恢复和强化系统时出现问题。 此方法的一个易于识别的组件是持续集成 （CI）/ 持续交付 （CD） 管道。 CI/CD 管道通过一系列步骤（包括生成、测试和部署）从提交中获取信息、代码和资源，以生成系统。 本文重点介绍在企业环境中在管道中有效地使用实验室的不同方法。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>在 DevOps 工作流中使用实验室的好处 

### <a name="focused-access"></a>重点访问 
使用实验室作为组件允许特定的生态系统与有限的群体相关联。 通常，在公共区域或特定功能中工作的团队或组将为其分配实验室。   

### <a name="infrastructure-replication-in-the-cloud"></a>云中的基础架构复制 
开发人员可以快速设置开发生态系统，其中包含包含源代码和工具的开发框。 开发人员还可以创建与生产配置几乎相同的环境。 它有助于更快的内环路开发。 

### <a name="pre-production"></a>预生产 
在 CI/CD 管道中建立实验室可以更轻松地让多个不同的预生产环境或计算机同时运行以进行异步测试。 可以在实验室内部署和管理不同的支持基础结构，如生成代理。 

## <a name="devops-with-devtest-labs"></a>使用开发人员测试实验室进行开发 

### <a name="development--operation"></a>开发/运营 
实验室应侧重于在要素区域中工作的团队。 此常见焦点允许共享特定于区域的资源，如工具、脚本或资源管理器模板。 它允许更快的更改，同时将负面影响限制为较小的组。 这些共享资源允许开发人员使用所有必要的代码、工具和配置创建开发 VM。 它们可以动态创建，也可以有一个系统，使用自定义创建基本映像。 开发人员不仅可以创建 VM，还可以根据必要的模板创建 DevTest Labs 环境，在实验室中创建适当的 Azure 资源。 任何更改或破坏性工作都可以针对实验室环境进行，而不会影响其他人。 请考虑产品是安装在客户计算机上的独立系统的情况。 在此方案中，DevTest Labs 改进了 VM 创建，包括使用工件安装其他软件，以及预构建客户配置，以便更快地对代码进行内部循环测试。 
  
## <a name="cicd-pipeline"></a>CI/CD 管道 
CI/CD 管道是 DevOps 中的关键组件之一，用于从开发人员的拉取请求中移动代码，将其与现有代码集成，并将其部署到生产生态系统。 所有资源不需要在实验室内。 例如，可以在实验室外部设置 Jenkins 主机，作为更持久的资源。 下面是将实验室集成到管道中的一些具体示例。 

### <a name="build"></a>构建 
生成管道的重点是创建一个组件包，这些组件将一起测试，以便移交给发布管道。 实验室可以作为生成代理和其他支持资源的位置作为生成管道的一部分。 能够动态构建基础结构可以进行更大的控制。 借助在实验室中具有多个环境的能力，可以在使用生成 ID 作为环境信息的一部分时异步运行每个生成，以唯一地标识特定生成的资源。   

对于生成代理，实验室限制访问的能力提高了安全性，并减少了意外损坏的可能性。  

### <a name="test"></a>测试 
DevTest Labs 允许 CI/CD 管道自动创建可用于自动和手动测试的 Azure 资源（VM、环境）。 VM 将使用使用生成过程中的信息创建项目或公式来创建测试所需的不同自定义配置。   

### <a name="release"></a>发布 
在部署代码之前，DevTest Labs 通常用于发布部分的验证。 它类似于在"生成"部分中的测试。 生产资源不应部署在 DevTest 实验室中。 

### <a name="customization"></a>自定义 
在 Azure DevOps 中，存在允许在特定实验室中操作 VM 和环境的现有任务。 虽然 Azure DevOps 服务是管理 CI/CD 管道的一种方式，但您可以将实验室集成到支持调用 REST API、执行 PowerShell 脚本或使用 Azure CLI 的任何系统。 

虽然某些 CI/CD 管道管理器具有现有的开源插件，可以管理 Azure 和 DevTest 实验室中的资源。 您可能需要使用一些自定义脚本来满足管道的特定需求。  考虑到这一点，在执行任务时，服务主体与适当的角色一起使用，以获得对实验室的访问。 服务主体通常需要对实验室的参与者角色访问权限。 有关详细信息，请参阅将[Azure 开发人员测试实验室集成到 Azure DevOps 持续集成和交付管道中](devtest-lab-integrate-ci-cd-vsts.md)。 
 