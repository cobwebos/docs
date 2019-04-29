---
title: 在 Azure 开发测试实验室中创建映像工厂 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中创建自定义映像工厂。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622611"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建自定义映像工厂
本文介绍如何使用示例脚本中可用设置的自定义映像工厂[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)。

## <a name="whats-an-image-factory"></a>映像工厂是什么？
映像工厂是一种配置即代码解决方案，生成和分发映像自动定期使用所有所需的配置。 映像工厂中的映像是始终保持最新，正在进行维护几乎为零后可以自动完成整个过程。 和，因为所有所需的配置已在映像中，它保存使用基本 OS 创建 VM 后手动配置系统中的时间。

若要获取开发人员桌面到开发测试实验室中处于就绪状态的重要加速器使用自定义映像。 自定义映像的缺点是，没有额外要保留在实验室中的内容。 例如，随着时间的推移过期产品的试用版 （或者） 不会应用新发布的安全更新，这迫使我们对定期刷新自定义映像。 使用映像工厂，必须签入源代码管理，并让自动化的过程生成基于定义的自定义映像的映像的定义。

该解决方案可从自定义映像创建虚拟机，同时消除其他日常维护成本的速度。 使用此解决方案中，可以自动创建自定义映像、 将它们分发给其他开发测试实验室，并停用旧的映像。 在以下视频中，您了解映像工厂，以及它与开发测试实验室的实现方式。  所有 Azure Powershell 脚本都是自由地可用且位于此处： [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory)。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>该解决方案的高级视图
该解决方案可从自定义映像创建虚拟机，同时消除其他日常维护成本的速度。 使用此解决方案中，可以自动创建自定义映像并将它们分发给其他开发测试实验室。 为自动执行开发测试实验室中的所有操作的业务流程引擎使用 Azure DevOps (以前称为 Visual Studio Team Services)。

![该解决方案的高级视图](./media/create-image-factory/high-level-view-of-solution.png)

没有[适用于开发测试实验室的 VSTS 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)，可用于执行这些单独的步骤：

- 创建自定义映像
- 创建 VM
- 删除 VM
- 创建环境
- 删除环境
- 填充环境

使用开发测试实验室扩展是轻松地开始使用开发测试实验室中自动创建自定义映像。

没有备用实现更复杂的方案中使用 PowerShell 脚本。 使用 PowerShell，可以完全自动执行映像工厂根据可在持续集成和持续交付 (CI/CD) 工具链的开发测试实验室。 遵循此备用解决方案中的原则是：

- 常用的更新应要求对映像工厂做任何更改。 （例如，添加新类型的自定义映像，自动停用旧的映像，添加新的终结点开发测试实验室，以接收自定义映像，依次类推。）
- 常见的更改受源代码管理 （基础结构即代码）
- 开发测试实验室接收自定义映像可能不在同一 Azure 订阅 （实验室跨订阅）
- PowerShell 脚本必须是可重复使用，因此我们可以启动其他工厂根据需要

## <a name="next-steps"></a>后续步骤
请继续学习本部分中的下一篇文章：[从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
