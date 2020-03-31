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
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561449"
---
# <a name="about-azure-devtest-labs"></a>关于 Azure 开发测试实验室
Azure 开发人员测试实验室使团队中的开发人员能够高效自管理虚拟机 （VM） 和 PaaS 资源，而无需等待批准。

DevTest 实验室创建由预配置的基础或 Azure 资源管理器模板组成的实验室。 这些工具和软件可用于创建环境。 您可以在几分钟内创建环境，而不是数小时或数天。

通过使用 DevTest Labs，您可以通过执行以下任务来测试应用程序的最新版本：

- 使用可重用的模板和工件快速预配 Windows 和 Linux 环境。
- 轻松地将部署管道与开发测试实验室集成，以预配按需环境。
- 通过预配多个测试代理并创建用于培训和演示的预配置环境，从而扩展负载测试。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
DevTest Labs 为使用 VM 的开发人员提供以下功能：

- 通过执行少于五个简单步骤，快速创建 VM。
- 从由团队领导或中央 IT 配置、批准和授权的 VM 基础的精选列表中选择。
- 从已安装所有软件和工具的预创建自定义映像创建 VM。 
- 从本质上是自定义映像的公式创建 VM，这些公式与创建 VM 时安装的软件的最新版本相结合。 
- 在 VM 预配后安装部署在 VM 上的扩展项目。
- 在 VM 上设置自动关闭和自动启动计划。
- 声明预创建的 VM 而不完成创建过程。

DevTest Labs 为使用 PaaS 环境的开发人员提供以下功能：

- 使用资源管理器通过执行少于三个简单步骤来快速创建 PaaS 环境。
- 从精心策划的资源管理器模板列表中进行选择，这些模板由团队领导或中央 IT 人员配置并授权。
- 通过使用资源管理器模板在实验室的上下文中浏览 Azure，将空资源组（沙盒）旋转。

DevTest Labs 还使中央 IT 部门能够控制浪费、优化资源成本，并通过执行以下任务保持预算范围：  

- 在 VM 上设置自动关闭和自动启动计划。
- 设置用户可以创建的 VM 数量的策略。
- 设置用户选择的 VM 大小和库图像的策略。
- 跟踪成本并设定实验室目标。
- 获得实验室高预计成本的通知，以便您可以采取必要的行动。

DevTest Labs 在创建、配置和管理云中的环境时提供了以下优势。

## <a name="cost-control-and-governance"></a>成本控制和治理
DevTest Labs 允许您执行以下任务，从而更轻松地控制成本：

- [在实验室上设置策略](devtest-lab-get-started-with-lab-policies.md)，例如每个用户或每个实验室的 VM 数。 
- 创建[策略以自动关闭](devtest-lab-set-lab-policy.md)和启动 VM。
- 跟踪 VM 和 PaaS 资源的成本，在实验室内启动，以保持在[您的预算](devtest-lab-configure-cost-management.md)之内。
- 保持在实验室的上下文中，这样您就不会在实验室之外启动资源。

## <a name="quickly-get-to-ready-to-test"></a>快速转至测试就绪
DevTest Labs 允许您创建预配置的环境，并配备您的团队开发和测试应用程序所需的一切。 只需[声明](devtest-lab-add-claimable-vm.md)安装应用程序最后一个良好版本的环境并开始工作。 或使用容器创建更快、更精简的环境。

## <a name="create-once-use-everywhere"></a>一次创建，随处可用
在团队或组织中捕获和共享 PaaS[环境模板](devtest-lab-create-environment-from-arm.md)和[项目](add-artifact-repository.md)（全部在源代码管理中），轻松创建开发人员和测试环境。

## <a name="worry-free-self-service"></a>无忧的自助服务
DevTest Labs 使开发人员和测试人员能够使用一组预配置的资源快速轻松地[创建 IaaS VM](devtest-lab-add-vm.md)和[PaaS 资源](devtest-lab-create-environment-from-arm.md)。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 资源 
开发人员还可以通过使用资源管理器模板来旋转 PaaS 资源，例如 Azure 服务结构群集、Azure 应用服务的 Web 应用功能和 SharePoint 服务器场。 要在实验室中开始使用 PaaS，请使用[公共环境存储库](devtest-lab-configure-use-public-environments.md)中的模板或[将实验室连接到您自己的 Git 存储库](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 您还可以跟踪这些资源的成本，以保持在预算范围内。

## <a name="integrate-with-your-existing-toolchain"></a>与现有工具链集成
使用预先制作的插件或 API 直接从您首选[的连续集成 （CI） 工具](devtest-lab-integrate-ci-cd-vsts.md)、集成开发环境 （IDE） 或自动发布管道中预配开发/测试环境。 您还可以使用全面的命令行工具。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 要了解有关 DevTest 实验室的更多内容，请参阅[开发人员测试实验室概念](devtest-lab-concepts.md)。
- 有关分步说明的演练，请参阅[教程：使用 Azure 开发人员测试实验室设置实验室](tutorial-create-custom-lab.md)。