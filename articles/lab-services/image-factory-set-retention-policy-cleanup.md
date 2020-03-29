---
title: 在 Azure 开发人员测试实验室中设置保留策略 |微软文档
description: 了解如何配置保留策略、清理工厂并从 DevTest 实验室停用旧映像。
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759408"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中设置保留策略
本文介绍设置保留策略、清理工厂以及从组织中的所有其他 DevTest 实验室停用旧映像。 

## <a name="prerequisites"></a>先决条件
在继续之前，请确保您已遵循了这些文章：

- [创建映像工厂](image-factory-create.md)
- [从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
- [保存自定义映像并分发到多个实验室](image-factory-save-distribute-custom-images.md)

以下项目应已经到位：

- Azure 开发人员测试实验室中映像工厂的实验室
- 一个或多个目标 Azure 开发人员测试实验室，工厂将在其中分发金色图像
- 用于自动执行映像工厂的 Azure DevOps 项目。
- 包含脚本和配置的源代码位置（在我们的示例中，在上面使用的同一 DevOps 项目中）
- 用于协调 Azure Powershell 任务生成定义
 
## <a name="setting-the-retention-policy"></a>设置保留策略
在配置清理步骤之前，请定义要在 DevTest 实验室中保留多少历史图像。 当您遵循 Azure DevOps 一文中的["运行映像工厂"](image-factory-set-up-devops-lab.md)时，将配置各种生成变量。 其中之一是**图像保留**。 将此变量设置为`1`，这意味着 DevTest 实验室不会维护自定义映像的历史记录。 只有最新的分布式图像可用。 如果将此变量更改为`2`，将保留最新的分布式图像以及以前的映像。 您可以设置此值以定义您希望在 DevTest Labs 中维护的历史图像数。

## <a name="cleaning-up-the-factory"></a>清理工厂
清理工厂的第一步是从映像工厂中删除金色图像 VM。 有一个脚本可以像我们以前的脚本一样执行此任务。 第一步是向生成定义添加另一个**Azure Powershell**任务，如下图所示：

![电源外壳步骤](./media/set-retention-policy-cleanup/powershell-step.png)

在列表中有新任务后，选择该项目并填写如下图像所示的所有详细信息：

![清理旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/configure-powershell-task.png)

脚本参数为： `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>停用旧图像 
此任务删除任何旧映像，仅保留与 Image**保留**生成变量匹配的历史记录。 向生成定义添加其他**Azure Powershell**生成任务。 添加任务后，选择任务并填写下图所示的详细信息： 

![停用旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/retire-old-image-task.png)

脚本参数为：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>对生成进行排队
现在已完成生成定义，可以排队生成，以确保一切正常工作。 生成成功完成后，新的自定义映像显示在目标实验室中，如果检查映像工厂实验室，则看不到预配的 VM。 此外，如果进一步排队生成，您将看到清理任务根据生成变量中设置的保留值从 DevTest Labs 中删除旧的自定义映像。

> [!NOTE]
> 如果在本系列最后一篇文章的末尾执行了生成管道，请手动删除在映像工厂实验室中创建的虚拟机，然后再排队生成。  仅在我们设置所有内容并验证其工作时，才需要手动清理步骤。



## <a name="summary"></a>总结
现在，您拥有了一个正在运行的映像工厂，可按需生成自定义映像并将其分发到实验室。 此时，只需正确设置图像并识别目标实验室。 如上一篇文章所述，位于 **"配置"** 文件夹中的**Labs.json**文件指定每个目标实验室都应提供哪些图像。 当您向组织添加其他 DevTest 实验室时，只需在新实验室的 Labs.json 中添加一个条目。

向工厂添加新映像也很简单。 如果要在工厂中包含新映像，请打开[Azure 门户](https://portal.azure.com)，导航到工厂 DevTest Labs，选择按钮添加 VM，然后选择所需的市场映像和项目。 选择 **"查看 Azure 资源管理器"模板**而不是选择 **"创建**"按钮，然后将模板另存为存储库中**GoldenImages**文件夹中的 .json 文件。 下次运行映像工厂时，它将创建自定义映像。


## <a name="next-steps"></a>后续步骤
1. [计划生成/发布](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer)定期运行映像工厂。 它定期刷新工厂生成的映像。
2. 为您的工厂制作更多金色图像。 您还可以考虑[创建项目](devtest-lab-artifact-author.md)来编写 VM 设置任务的其他部分，并在出厂映像中包括工件。
4. 创建[单独的生成/版本](/azure/devops/pipelines/overview?view=azure-devops-2019)以单独运行**分发图像**脚本。 当您对 Labs.json 进行更改时，可以运行此脚本，并将图像复制到目标实验室，而无需再次重新创建所有图像。

