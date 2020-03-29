---
title: 在 Azure 开发人员测试实验室中创建映像工厂 |微软文档
description: 本文介绍如何使用 Git 存储库（Azure DevTest Labs）中提供的示例脚本来设置自定义映像工厂。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759442"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中创建自定义映像工厂
本文介绍如何使用[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中的示例脚本来设置自定义映像工厂。

## <a name="whats-an-image-factory"></a>什么是图像工厂？
映像工厂是一种按代码配置的解决方案，它定期使用所有所需的配置自动生成和分发映像。 映像工厂中的图像始终处于最新状态，一旦整个过程自动化，持续维护几乎为零。 而且，由于所有必需的配置都已在映像中，因此在使用基本操作系统创建 VM 后，它节省了手动配置系统的时间。

在 DevTest Labs 中，将开发人员桌面安装到就绪状态的重要加速器是使用自定义映像。 自定义图像的缺点是实验室中还有额外的维护功能。 例如，产品试用版会随着时间的推移过期（或）未应用新发布的安全更新，这迫使我们定期刷新自定义映像。 使用映像工厂，您将映像的定义签入到源代码控制，并具有根据定义生成自定义映像的自动化过程。

该解决方案可实现从自定义映像创建虚拟机的速度，同时消除额外的持续维护成本。 使用此解决方案，您可以自动创建自定义映像、将其分发到其他 DevTest 实验室，并停用旧映像。 在下面的视频中，您将了解映像工厂以及如何使用 DevTest Labs 实现映像工厂。  所有 Azure Powershell 脚本都免费提供，并位于[https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)此处： 。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解决方案的高级视图
该解决方案可实现从自定义映像创建虚拟机的速度，同时消除额外的持续维护成本。 使用此解决方案，您可以自动创建自定义映像并将其分发到其他 DevTest 实验室。 您可以使用 Azure DevOps（以前的可视化工作室团队服务）作为业务流程引擎，用于自动执行 DevTest 实验室中的所有操作。

![解决方案的高级视图](./media/create-image-factory/high-level-view-of-solution.png)

DevTest Labs 的[VSTS 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)使您能够执行以下各个步骤：

- 创建自定义映像
- 创建 VM
- 删除 VM
- 创建环境
- 删除环境
- 填充环境

使用 DevTest Labs 扩展是在 DevTest 实验室中自动创建自定义映像的简便方法。

对于更复杂的方案，使用 PowerShell 脚本有一个备用实现。 使用 PowerShell，您可以完全自动化基于 DevTest Labs 的图像工厂，该实验室可用于您的持续集成和连续交付 （CI/CD） 工具链。 此备用解决方案遵循的原则是：

- 常见更新不应对映像工厂进行任何更改。 （例如，添加新类型的自定义映像、自动停用旧映像、添加新的"终结点"DevTest Labs 以接收自定义映像等。
- 常见更改由源代码控制（基础结构作为代码）支持
- 接收自定义映像的开发人员测试实验室可能不在同一 Azure 订阅中（实验室跨订阅）
- PowerShell 脚本必须是可重用的，以便我们可以根据需要启动其他工厂

## <a name="next-steps"></a>后续步骤
继续学习本节中的下一篇文章：从[Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
