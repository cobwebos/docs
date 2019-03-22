---
title: 关于 Azure 开发测试实验室 | Microsoft Docs
description: 了解开发测试实验室如何轻松地创建、管理和监视 Azure 虚拟机
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339323"
---
# <a name="about-azure-devtest-labs"></a>关于 Azure 开发测试实验室
Azure 开发测试实验室是一个服务，使开发人员有效地自助服务虚拟机和/或所需的开发、 测试、 培训和演示等，而无需等待所需的工具的常量审批的 PaaS 资源团队。 

实验室已包含预配置的基或具有所有必需工具和开发人员可用于创建环境的软件资源管理器模板。 开发人员可以在几分钟内，而不是小时或天创建其环境。 

使用开发测试实验室，您可以通过执行以下任务来测试你的应用程序的最新版本：

- 使用可重用模板和项目快速预配 Windows 和 Linux 环境
- 将你的部署管道与开发测试实验室来预配按需环境轻松集成
- 纵向扩展负载测试的预配多个测试代理，并创建预配为培训和演示环境。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>功能
开发测试实验室为开发人员使用的虚拟机提供以下功能：

- 通过以下低于五个简单步骤快速创建虚拟机。
- 从 VM 基项的配置、 批准并授权的团队主管或中部的特选列表中选择 IT。
- 从预先创建的所有软件和工具安装在映像中的自定义映像创建计算机。 
- 从本质上自定义映像和安装在虚拟机的创建时间的软件的最新版本的公式创建机。
- 安装后对其进行设置，在 VM 上部署的扩展插件的项目。
- 设置自动关闭和自动关闭已在一天结束和乃至更高版本的计算机上的开始计划和运行第二天早晨。
- 只需声明预先创建的虚拟机，而无需经历的机创建过程。 

开发测试实验室为开发人员使用 PaaS 环境提供以下功能：

- 创建 PaaS Azure 资源管理器环境中快速小于三个简单步骤。
- 从资源管理器模板，其配置、 批准并授权的团队主管或中部的特选列表中选择 IT。
- 启动使用资源管理器模板时仍可实现在实验室的上下文中浏览整个 Azure 的空资源组 （沙盒）。
- 开发测试实验室为创建、配置和管理开发人员和云中的测试环境提供了以下优势

除了在团队的开发人员的自助服务模型，该服务可以让中央 IT 部门控制会浪费、 优化资源成本并保持在预算范围内通过执行以下任务： 

- 设置自动关闭和自动启动虚拟机上的计划。
- 将策略设置上的虚拟机用户数会创建。
- 从中进行挑选映像的用户的虚拟机大小和库上设置策略。
- 跟踪实验室成本，并设置目标。
- 获取通知上的实验室的预计成本高，以便您可以采取必要的操作。 

开发测试实验室提供了创建、 配置和管理云中的环境中以下优势：

## <a name="cost-control-and-governance"></a>成本的控制和管理
开发测试实验室允许您执行以下任务可更轻松地控制成本：

- 在实验室-例如数量的虚拟机 (VM) 上设置策略，每个用户和每个实验室的 Vm 数。 
- 创建策略来自动关闭和启动 Vm。
- 可以对虚拟机和 PaaS 资源，以便可以保留在你预定义的预算内启动在你的实验室内跟踪成本。 
- 可帮助开发人员，以便它们不会得到快速启动它的基础的资源组或订阅外部的任何资源保留在实验室的上下文内。

## <a name="quickly-get-to-ready-to-test"></a>快速转至测试就绪
通过开发测试实验室可以创建预配置的环境，其中包含团队需要开始开发和测试应用程序的所有内容。 只需声明安装应用程序的最后一次的良好生成环境和开始立即使用。 或者，使用环境容器来创建更加快速和高效的环境。

## <a name="create-once-use-everywhere"></a>一次创建，随处可用
捕获并共享 PaaS 环境模板和项目内你的团队或组织的所有源代码管理中的以开发人员轻松创建和测试环境。

## <a name="worry-free-self-service"></a>无忧的自助服务
开发测试实验室快速启用开发人员和测试人员，并轻松地创建虚拟机 (IaaS) 和 PaaS 资源中单击几下，使用一组预配置为它们的资源。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 资源 
开发测试实验室还使开发人员能够快速创建 PaaS 资源，例如 Web 应用、 Service Fabric 群集，SharePoint 场，依次类推。 在实验室内使用资源管理器模板。 从我们公共环境的存储库使用资源管理器模板或实验室连接到你自己的 Git 存储库，以在实验室中开启 PaaS。 此外可以在这些资源，以保留在你预定义的预算内跟踪成本。 

## <a name="integrate-with-your-existing-toolchain"></a>与你的现有工具链进行集成
使用预先做好的插件或我们的 API 预配开发/测试环境，直接从你喜欢的持续集成 (CI) 工具、 集成开发环境 (IDE) 或自动发布管道。 还可以使用综合命令行工具。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- 若要了解有关开发测试实验室的详细信息，请参阅[开发测试实验室概念](devtest-lab-concepts.md)
- 有关分步说明演练，请参阅[教程：使用 Azure 开发测试实验室设置实验室](tutorial-create-custom-lab.md)


