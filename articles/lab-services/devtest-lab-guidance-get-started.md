---
title: 开始使用 Azure 开发测试实验室
description: 本文提供了使用 Azure 开发测试实验室的主要场景以及开始在你的组织中使用该服务的两条常规路径。
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
ms.openlocfilehash: 21837265a963829539f12cf7911191a61be43fbc
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243035"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>开始使用 Azure 开发测试实验室
在你决定探究开发测试实验室后，可以沿两条常规路径继续前进 – 概念验证与按比例部署。 

**规模化部署**包括数周/数月的审查和规划，其目的是将开发测试实验室部署到具有数百或数千个开发人员的整个企业。 

**概念验证部署**专注于集中单个团队的力量来实现组织价值。 虽然想想规模化部署比较诱人，但是该方法通常比概念验证选项更容易失败。 因此，我们建议从小型部署开始，从第一个团队那里学习经验，对两到三个另外的团队重复相同的方法，然后根据所获得的知识规划规模化部署。 为实现成功的概念验证，我们建议你挑选一两个团队，识别其方案（开发环境与测试环境），记录其当前的使用案例，并部署开发测试实验室。 

## <a name="scenarios"></a>方案
开发测试实验室实现有三种主要方案： 

- 开发人员桌面
- 测试环境
- 实验室/培训/黑客松

## <a name="developer-desktops"></a>开发人员桌面
开发人员针对不同的项目对开发计算机有不同的要求。 使用开发测试实验室，开发人员可以访问预先配置的按需 VM 来满足他们最常用的方案。 开发测试实验室具有以下优点：

- 组织可以提供在各个团队之间确保一致性的常用开发和测试环境
- 开发人员可以快速按需预配其开发计算机
- 开发人员能够以自助方式预配资源，不需要具有订阅级权限
- IT 或管理员可以预先定义网络拓扑，并且开发人员能够直接以简单直观的方式利用它，不需要任何特殊访问
- 开发人员可以根据需要轻松自定义其开发计算机
- 管理员可以控制成本，只需确保：
    - 开发人员可获取的 VM 数量不能超过开发所需的 VM 数量
    - VM 在不使用时关闭
    - 仅允许为特定的工作负荷使用一部分 VM 实例大小

## <a name="test-environments"></a>测试环境
在整个企业中创建和管理测试环境可能需要做大量工作。 使用开发测试实验室，可以轻松创建、更新或复制测试环境，这使得团队在需要时可以访问完全配置的环境。 在此方案中，开发测试实验室具有以下优势：

- 测试人员可使用可重用模板和项目快速预配 Windows 和 Linux 环境，从而测试最新版应用程序。
- 测试人员可以预配多个测试代理，从而纵向扩展负载测试。
- 管理员可以将实验室连接到 Azure DevOps 来实现 DevOps 方案
- 管理员可以控制成本，只需确保：
    - 测试人员可获取的 VM 数量不能超过所需数量
    - VM 在不使用时关闭
    - 仅允许为特定的工作负荷使用一部分 VM 实例大小

## <a name="labs-workshops-trainings-and-hackathons"></a>实验室、研讨会、培训和黑客松  
Azure 开发测试实验室中的实验室是很棒的容器，用于容纳暂时性的活动，例如研讨会、动手实验室、培训和黑客松。 此服务允许你创建实验室，可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 可应用策略以确保为每个学员提供培训环境（需要时）并包含培训所需的足够资源（例如虚拟机）。 最后，可轻松与学员共享实验室（学员通过单击即可访问）。 在课程结束后，可以轻松删除实验室和所有相关的资源。


## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[纵向扩展开发测试实验室部署](devtest-lab-guidance-scale.md)
