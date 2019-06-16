---
title: Azure 开发测试实验室和 DevOps 的集成 |Microsoft Docs
description: 了解如何使用 Azure 开发测试实验室的实验室中持续集成 (CI) / 持续交付 (CD) 管道在企业环境中。
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078919"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Azure 开发测试实验室与 Azure DevOps 集成
DevOps 是与运营 (Ops) 系统集成软件开发 (Dev) 的软件开发方法。 此系统可能会提供新功能、 更新和修补程序符合业务目标。 此方法涉及到从设计基于目标、 使用模式和客户反馈; 的新功能修复、 恢复和强化系统出现问题时。 这种方法轻松识别的组件是持续集成 (CI) / 持续交付 (CD) 管道。 CI/CD 管道将信息、 代码和通过一系列步骤，包括生成、 测试和部署，以生成系统的提交中的资源。 本文重点介绍不同的方式，有效地在企业环境中使用在管道中的实验室。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>DevOps 工作流中使用实验室的好处 

### <a name="focused-access"></a>有针对性的访问 
作为一个组件使用实验室允许特定的生态系统，以将与一组有限的用户相关联。 通常情况下，团队或工作中常见的区域或特定功能的组将拥有分配给他们的实验室。   

### <a name="infrastructure-replication-in-the-cloud"></a>在云中的基础结构复制 
开发人员可以快速设置包括源代码和工具的开发框开发生态系统。 开发人员还可以创建几乎等同于生产配置的环境。 它有助于更快地内部循环开发。 

### <a name="pre-production"></a>预生产 
CI/CD 管道中拥有实验室便于有多个不同的预生产环境或在同一时间进行异步测试运行的计算机。 可以部署和管理实验室中不同的支持基础结构，如生成代理。 

## <a name="devops-with-devtest-labs"></a>开发测试实验室中的 DevOps 

### <a name="development--operation"></a>开发 / 操作 
实验室应关注在功能区中工作的团队。 此常见焦点允许共享的特定于区域的资源，如工具、 脚本或资源管理器模板。 限制为较小的组的负面影响时，它允许更快的更改。 这些共享的资源允许开发人员若要创建开发 Vm 具有所有必要的代码、 工具和配置。 它们可以是动态创建或使用的是创建基本映像进行自定义的系统。 不仅可以开发人员创建的 Vm，而且它们还可以创建基于这些必要的模板，以便在实验室中创建相应的 Azure 资源的开发测试实验室环境。 任何更改或破坏性的工作可以进行针对实验室环境而不会影响其他任何人。 请考虑该产品的客户的计算机上安装的独立系统的情况。 此方案中，开发测试实验室进行改进，包括安装其他软件使用项目和预生成的客户配置会更快的内部循环测试代码的 VM 创建。 
  
## <a name="cicd-pipeline"></a>CI/CD 管道 
CI/CD 管道是 DevOps 的移动开发者的拉取请求中的代码与现有代码中，并将其部署到生产生态系统中的关键组件之一。 无需为实验室中的所有资源。 例如，Jenkins 主机可能会将设置外部实验室为更持久资源。 以下是一些将实验室集成到管道的特定示例。 

### <a name="build"></a>Build 
生成管道侧重于创建的包进行测试在一起以便移交给发布管道的组件。 实验室可以为生成代理和其他支持资源的位置是生成管道的一部分。 使能够动态构建基础结构可以更好地控制。 能够在实验室中有多个环境，每个生成可以同时使用的生成 ID 作为环境信息的一部分来唯一地标识为特定生成的资源以异步方式运行。   

对于生成的代理，限制访问实验室的功能可提高安全性并减少了意外损坏的可能性。  

### <a name="test"></a>测试 
开发测试实验室允许自动可用于为自动和手动测试的 Azure 资源 （虚拟机、 环境） 创建一个 CI/CD 管道。 将使用项目或使用生成过程中的信息来创建不同的自定义配置进行测试所需的公式创建的 Vm。   

### <a name="release"></a>发行版本 
开发测试实验室通常用于验证在发布部分中之前的代码部署。 它将类似于测试的生成部分。 不应在开发测试实验室中部署生产资源。 

### <a name="customization"></a>自定义 
Azure DevOps 中有允许的 Vm 的操作和在特定的实验室环境的现有任务。 虽然 Azure DevOps 服务是一种方法来管理在 CI/CD 管道，您可以将实验室集成到任何支持的功能，可以调用 REST Api、 执行 PowerShell 脚本，或使用 Azure CLI 的系统。 

虽然一些 CI/CD 管道管理器具有现有的开放源代码插件，可以管理 Azure 和开发测试实验室中的资源。 您可能需要使用一些自定义脚本以满足特定需求的管道。  这一点后，执行任务时，服务主体用于相应的角色才能访问实验室。 服务主体通常需要对实验室的参与者角色访问权限。 有关详细信息，请参阅[到 Azure DevOps 持续集成和交付管道中集成 Azure DevTests 实验室](devtest-lab-integrate-ci-cd-vsts.md)。 
 