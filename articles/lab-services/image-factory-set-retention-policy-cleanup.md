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
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622524"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建自定义映像工厂
本文介绍如何设置保留策略、 清理工厂，并从组织中的所有开发测试实验室中停用旧的映像。 

## <a name="prerequisites"></a>必备组件
请确保在继续操作之前遵循以下文章：

- [创建映像工厂](image-factory-create.md)
- [从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
- [保存自定义映像并将分发到多个实验室](image-factory-save-distribute-custom-images.md)

以下各项应已就位：

- 在 Azure 开发测试实验室中的图像工厂实验室
- 一个或多个目标的工厂将黄金映像分发的 Azure 开发测试实验室
- Azure DevOps 项目用于自动执行映像工厂。
- 源代码位置包含脚本和配置 （在本示例中，在上面所用的同一 DevOps 项目中）
- 生成定义来安排 Azure Powershell 任务
 
## <a name="setting-the-retention-policy"></a>设置保留策略
配置清理步骤之前，请定义你想要在开发测试实验室中保留多少历史映像。 当你按照[从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)文章中，配置各种生成变量。 其中任一**ImageRetention**。 将此变量设置为`1`，这意味着开发测试实验室将不维护自定义映像的历史记录。 只有最新的分布式的映像可用。 如果将此变量为`2`、 最新分布式映像加上以前的将保持不变。 可以设置此值来定义你希望在开发测试实验室中维护的历史映像数量。

## <a name="cleaning-up-the-factory"></a>清理工厂
清理工厂的第一步是从映像工厂删除黄金映像 Vm。 没有一个脚本来执行此任务就像我们先前的脚本。 第一步是添加另一个**Azure Powershell**任务到生成定义，如在下图中所示：

![PowerShell 步骤](./media/set-retention-policy-cleanup/powershell-step.png)

后必须将新的任务列表中，选择项，并填写所有详细信息，如在下图中所示：

![清理旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/configure-powershell-task.png)

脚本参数是： `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>停用旧的映像 
此任务将删除任何旧的映像，使仅历史记录匹配**ImageRetention**生成变量。 添加其他**Azure Powershell**构建到我们的生成定义的任务。 它添加后，选择该任务，并填写详细信息，如在下图中所示： 

![停用旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/retire-old-image-task.png)

脚本参数包括： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>对生成进行排队
现在，已完成的生成定义，以确保一切正常对新生成进行排队。 新的自定义映像显示在目标实验室中，如果您查看图像工厂实验室成功完成生成后，您会看到任何预配的 Vm。 而且如果排队后续生成时，将看到停用旧的自定义映像从开发测试实验室中根据生成变量中设置的保留期值的清理任务。

> [!NOTE]
> 如果序列中执行的最后一篇文章末尾生成管道，手动删除虚拟机已在映像工厂实验室中创建新生成排队之前。  我们完成所有设置和验证它正常工作时，才需要手动清理步骤。



## <a name="summary"></a>摘要
现在您有正在运行的映像工厂可生成并分发到按需在实验室的自定义映像。 在此点，它的正确设置了只需获取你的映像和确定目标实验室。 在上一篇文章中所述**Labs.json**文件位于你**配置**文件夹指定的映像应在每个目标实验室可。 其他开发测试实验室添加到你的组织时，只需为新的实验室 Labs.json 中添加的条目。

将新映像添加到您的工厂也非常简单。 当你想要打开在工厂中包括新的映像[Azure 门户](https://portal.azure.com)、 导航到您的工厂开发测试实验室，选择此按钮以添加 VM，并选择所需的 marketplace 映像和项目。 而不是选择**创建**按钮以使新的 VM，选择**查看 Azure 资源管理器模板**"并将模板保存为.json 文件某个位置内**GoldenImages**存储库中的文件夹。 在下次运行您的图像工厂，它将创建自定义映像。


## <a name="next-steps"></a>后续步骤
1. [计划生成/发布](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer)定期运行映像工厂。 它将刷新定期在工厂生成的图像。
2. 请为您的工厂的更多黄金映像。 您也可以考虑[创建项目](devtest-lab-artifact-author.md)编写脚本的 VM 安装程序任务的其他部分，并将这些项目包含在工厂映像。
4. 创建[单独的生成/发布](/azure/devops/pipelines/overview?view=azure-devops-2019)运行**DistributeImages**单独脚本。 对 Labs.json 进行更改并获取映像复制到目标实验室而无需再次重新创建所有映像时，可以运行此脚本。

