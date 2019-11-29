---
title: 在 Azure 开发测试实验室中创建映像工厂 |Microsoft Docs
description: 了解如何使用 Git 存储库中提供的示例脚本设置自定义映像工厂。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 7779914d9681d0f80cab9568da6a20b15e3a2eb1
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560009"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建自定义映像工厂
本文介绍如何使用[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中提供的示例脚本设置自定义映像工厂。

## <a name="whats-an-image-factory"></a>什么是映像工厂？
映像工厂是一个配置即代码解决方案，它使用所有所需配置定期自动生成和分发映像。 映像工厂中的映像始终是最新的，并且在整个过程自动完成后，当前的维护几乎为零。 并且，由于所有必需的配置都已在映像中，因此，在使用基准 OS 创建 VM 后，它将节省手动配置系统的时间。

使开发人员桌面在开发测试实验室中处于就绪状态的重要加速器是使用自定义映像。 自定义映像的缺点在于，实验室有一些额外的维护。 例如，产品的试用版将在一段时间后过期（或），新发布的安全更新不会应用，这会强制我们定期刷新自定义映像。 使用映像工厂时，可以将映像的定义签入到源代码控制中，并具有基于定义生成自定义映像的自动过程。

此解决方案实现了从自定义映像创建虚拟机的速度，同时消除了额外的维护成本。 利用此解决方案，可以自动创建自定义映像，将其分发给其他开发测试实验室，并停用旧映像。 在下面的视频中，你将了解映像工厂，以及如何通过开发测试实验室实现它。  所有 Azure Powershell 脚本都可免费使用，如下所示： [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解决方案的高级视图
此解决方案实现了从自定义映像创建虚拟机的速度，同时消除了额外的维护成本。 利用此解决方案，你可以自动创建自定义映像并将其分发给其他开发测试实验室。 使用 Azure DevOps （以前 Visual Studio Team Services）作为业务流程引擎，以便在开发测试实验室中自动执行所有操作。

![解决方案的高级视图](./media/create-image-factory/high-level-view-of-solution.png)

有一个[用于开发测试实验室的 VSTS 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)，可用于执行以下各个步骤：

- 创建自定义映像
- 创建 VM
- 删除 VM
- 创建环境
- 删除环境
- 填充环境

使用开发测试实验室扩展，可以轻松地开始在开发测试实验室中自动创建自定义映像。

对于更复杂的方案，可使用 PowerShell 脚本进行备用实现。 使用 PowerShell，可以根据可在持续集成和持续交付（CI/CD）工具链中使用的开发测试实验室完全自动执行映像工厂。 本备用解决方案中遵循的原则包括：

- 常见更新不需要更改映像工厂。 （例如，添加新类型的自定义映像，自动停用旧映像，添加新的 "终结点" 开发测试实验室来接收自定义图像等。）
- 源代码管理（基础结构即代码）支持常见更改
- 接收自定义映像的开发测试实验室可能不在同一 Azure 订阅中（实验室跨订阅）
- PowerShell 脚本必须可重复使用，因此我们可以根据需要加速其他工厂

## <a name="next-steps"></a>后续步骤
转到本部分中的下一篇文章：[从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
