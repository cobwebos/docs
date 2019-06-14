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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561449"
---
# <a name="about-azure-devtest-labs"></a>关于 Azure 开发测试实验室
Azure 开发测试实验室允许在团队的开发人员有效地自行管理虚拟机 (Vm) 和 PaaS 资源而无需等待审批。

开发测试实验室创建实验室组成预配置的库或 Azure 资源管理器模板。 这些具有所有必需工具和可用于创建环境的软件。 你可以在几分钟，而不是小时或天中创建环境。

通过使用开发测试实验室，可以通过执行以下任务来测试您的应用程序的最新版本：

- 快速预配 Windows 和 Linux 环境通过使用可重用模板和项目。
- 轻松地将部署管道与开发测试实验室集成，以预配按需环境。
- 纵向扩展负载测试的预配多个测试代理，并创建预配为培训和演示环境。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
开发测试实验室为开发人员使用的 Vm 提供以下功能：

- 通过以下少于五个简单步骤快速创建 Vm。
- 从 VM 基项的配置、 批准并授权的团队主管或中部的特选列表中选择 IT。
- 从预先创建的所有软件和工具已安装的自定义映像创建 Vm。 
- 从本质上自定义映像与创建 Vm 时，会安装该软件的最新版本结合使用的公式创建 Vm。 
- 安装扩展后预配后，虚拟机上部署的项目。
- 设置自动关闭和自动启动虚拟机上的计划。
- 无需通过创建过程中声明的预先创建的 VM。

开发测试实验室为开发人员使用 PaaS 环境提供以下功能：

- 使用资源管理器通过以下三个简单步骤快速创建 PaaS 环境。
- 从资源管理器模板，该配置，并授权的团队主管或中部的特选列表中选择 IT。
- 通过使用资源管理器模板以探索 Azure 实验室的上下文中启动的空资源组 （沙盒）。

开发测试实验室还使中心 IT 部门控制会浪费、 优化成本上的资源，并保持在预算范围内通过执行以下任务：  

- 在 Vm 上设置自动关闭和自动启动的计划。
- 设置策略的用户可以创建的 Vm 数。
- 设置 Vm 的大小和库映像，供用户选择的策略。
- 跟踪实验室成本，并设置目标。
- 获取实验室的预计成本高上收到通知，以便您可以采取必要的操作。

开发测试实验室提供以下优势中创建、 配置和管理云中的环境。

## <a name="cost-control-and-governance"></a>成本的控制和管理
开发测试实验室允许您执行以下任务可更轻松地控制成本：

- [在您的实验室设置策略](devtest-lab-get-started-with-lab-policies.md)，如每个用户或每个实验室的 Vm 数。 
- 创建[策略来自动关闭](devtest-lab-set-lab-policy.md)和启动 Vm。
- 在 Vm 和 PaaS 资源跟踪成本在范围内的实验室内向上纺成[你的预算](devtest-lab-configure-cost-management.md)。
- 保持您的实验室的上下文中，因此你不加速这些外部资源。

## <a name="quickly-get-to-ready-to-test"></a>快速转至测试就绪
开发测试实验室可以创建预配的环境配备有你的团队需要用于开发和测试应用程序的所有内容。 只需[对环境提出](devtest-lab-add-claimable-vm.md)上次正常生成的应用程序的安装位置并开始工作。 或使用环境容器来实现更快、 更精简的环境的创建。

## <a name="create-once-use-everywhere"></a>一次创建，随处可用
捕获并共享 PaaS[环境模板](devtest-lab-create-environment-from-arm.md)和[项目](add-artifact-repository.md)团队或组织内，尽在源代码控制 — 轻松地创建开发人员和测试环境。

## <a name="worry-free-self-service"></a>无忧的自助服务
开发测试实验室，开发人员和测试人员到快速、 轻松地[创建 IaaS Vm](devtest-lab-add-vm.md)并[PaaS 资源](devtest-lab-create-environment-from-arm.md)使用一组预配置的资源。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 资源 
开发人员可以也加速 PaaS 资源，例如 Azure Service Fabric 群集的 Azure 应用服务和 SharePoint 场的 Web 应用功能使用资源管理器模板。 若要开始在实验室中的 PaaS 上，使用中的模板[公共环境存储库](devtest-lab-configure-use-public-environments.md)或[实验室连接到 Git 存储库](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 此外可以在这些资源，以在预算内跟踪成本。

## <a name="integrate-with-your-existing-toolchain"></a>与你的现有工具链进行集成
使用预先制作的插件或 API 预配开发/测试环境直接从偏好[持续集成 (CI) 工具](devtest-lab-integrate-ci-cd-vsts.md)、 集成开发环境 (IDE) 或自动发布管道。 此外可以使用综合命令行工具。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要了解有关开发测试实验室的详细信息，请参阅[开发测试实验室概念](devtest-lab-concepts.md)。
- 有关分步说明演练，请参阅[教程：使用 Azure 开发测试实验室设置实验室](tutorial-create-custom-lab.md)。