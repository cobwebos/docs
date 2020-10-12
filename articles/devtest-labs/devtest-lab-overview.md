---
title: 关于 Azure 开发测试实验室 | Microsoft Docs
description: 了解开发测试实验室如何轻松地创建、管理和监视 Azure 虚拟机
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480195"
---
# <a name="about-azure-devtest-labs"></a>关于 Azure 开发测试实验室
借助 Azure 开发测试实验室，团队开发人员无需等待批准即可 (Vm) 和 PaaS 资源高效地自行管理虚拟机。

开发测试实验室创建实验室，其中包含预配置的基版或 Azure 资源管理器模板。 其中包含可用于创建环境的所有必需工具和软件。 你可以在几分钟内创建环境，而不是几分钟或几天。

通过使用开发测试实验室，你可以通过执行以下任务来测试应用程序的最新版本：

- 使用可重用的模板和项目快速预配 Windows 和 Linux 环境。
- 轻松地将部署管道与开发测试实验室集成，以预配按需环境。
- 通过预配多个测试代理并创建预配环境进行定型和演示，增加负载测试。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
开发测试实验室为使用 Vm 的开发人员提供了以下功能：

- 按照五个简单的步骤操作，快速创建 Vm。
- 从由团队主管或中心人员配置、批准和授权的 VM 基础的特选列表中进行选择。
- 使用预先创建的自定义映像创建 Vm，其中已安装所有软件和工具。 
- 从本质上是自定义映像的公式创建 Vm，并将其与创建 Vm 时所安装的软件的最新版本组合在一起。 
- 在预配 Vm 后，在 Vm 上安装扩展的项目。
- 为 Vm 设置自动关闭和自动启动计划。
- 在不完成创建过程的情况下，声明预创建的 VM。

开发测试实验室向使用 PaaS 环境的开发人员提供了以下功能：

- 通过执行以下三个简单步骤，使用资源管理器快速创建 PaaS 环境。
- 从已配置的资源管理器模板列表中进行选择，并由团队主管或中心 IT 授权。
- 通过使用资源管理器模板在实验室上下文内浏览 Azure，加速 (沙盒) 的空资源组。

开发测试实验室还可通过执行以下任务来控制浪费、优化资源的成本并保持在预算内：  

- 为 Vm 设置自动关闭和自动启动计划。
- 设置用户可创建的 Vm 数的策略。
- 对 Vm 的大小和库图像设置策略，用户可以从中选择。
- 在实验室上跟踪成本和设置目标。
- 获得有关实验室预计成本的通知，以便你可以采取必要的措施。

在云中创建、配置和管理环境时，开发测试实验室具有以下优势。

## <a name="cost-control-and-governance"></a>成本控制和管理
借助开发测试实验室，你可以执行以下任务，从而更轻松地控制成本：

- [在实验室中设置策略](devtest-lab-set-lab-policy.md)，例如每个用户或每个实验室的 vm 数。 
- 创建 [自动](devtest-lab-set-lab-policy.md) 关闭和启动 vm 的策略。
- 跟踪 Vm 和 PaaS 内的 PaaS 资源的成本，使其保持在 [预算](devtest-lab-configure-cost-management.md)内。
- 在实验室环境中保持不变，这样就不会占用它们。

## <a name="quickly-get-to-ready-to-test"></a>快速转至测试就绪
借助开发测试实验室，你可以创建预配的环境，该环境具备团队开发和测试应用程序所需的一切。 只需声明安装了应用程序的最后一个良好版本并开始工作的 [环境](devtest-lab-add-claimable-vm.md) 即可。 或者使用容器更快、更精简的环境创建。

## <a name="create-once-use-everywhere"></a>一次创建，随处可用
在你的团队或组织中捕获和共享 PaaS [环境模板](devtest-lab-create-environment-from-arm.md) 和 [项目](add-artifact-repository.md) -所有这些都在源代码管理中，可以轻松地创建开发人员和测试环境。

## <a name="worry-free-self-service"></a>无忧的自助服务
使用开发测试实验室，开发人员和测试人员可以通过使用一组预配置的资源快速轻松地 [创建 IaaS vm](devtest-lab-add-vm.md) 和 [PaaS 资源](devtest-lab-create-environment-from-arm.md) 。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 资源 
开发人员还可以使用资源管理器模板来加速 PaaS 资源，如 Azure Service Fabric 群集、Azure App Service 和 SharePoint 场的 Web 应用功能。 若要在 PaaS 实验室中开始使用，请使用 [公共环境存储库](devtest-lab-configure-use-public-environments.md) 中的模板，或将 [实验室连接到自己的 Git 存储库](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 你还可以跟踪这些资源的成本，使其保持在预算内。

## <a name="integrate-with-your-existing-toolchain"></a>与现有的工具链集成
使用预置的插件或 API 直接从你喜欢的 [持续集成 (CI) 工具](devtest-lab-integrate-ci-cd.md)、集成开发环境 (IDE) 或自动发布管道设置开发/测试环境。 你还可以使用全面的命令行工具。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要了解有关开发测试实验室的详细信息，请参阅 [开发测试实验室概念](devtest-lab-concepts.md)。
- 有关分步说明的演练，请参阅 [教程：使用 Azure 开发测试实验室设置实验室](tutorial-create-custom-lab.md)。