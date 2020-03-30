---
title: 在 Azure 开发人员测试实验室中保存和分发映像 |微软文档
description: 本文提供了从 Azure DevTest Labs 中已创建的虚拟机 （VM） 中保存自定义映像的步骤。
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759425"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>保存自定义映像并分发到多个实验室
本文提供了从已创建的虚拟机 （VM） 中保存自定义映像的步骤。 它还介绍了如何将这些自定义映像分发到组织中的其他 DevTest 实验室。

## <a name="prerequisites"></a>先决条件
以下项目应已经到位：

- Azure 开发人员测试实验室中映像工厂的实验室。
- 用于自动执行映像工厂的 Azure DevOps 项目。
- 包含脚本和配置的源代码位置（在我们的示例中，在上一步骤中提到的同一 DevOps 项目中）。
- 生成定义以协调 Azure Powershell 任务。

如果需要，请按照 Azure [DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)中的步骤创建或设置这些项目。 

## <a name="save-vms-as-generalized-vhds"></a>将 VM 保存为通用 VHD
将现有 VM 保存为通用 VHD。  有一个示例 PowerShell 脚本，用于将现有 VM 保存为通用 VHD。 要使用它，首先，将另一个**Azure Powershell**任务添加到生成定义中，如下图所示：

![添加 Azure 电源外壳步骤](./media/save-distribute-custom-images/powershell-step.png)

在列表中有新任务后，选择该项目，以便我们可以填写如下图像所示的所有详细信息： 

![电源壳设置](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>通用映像与专用自定义映像
在[Azure 门户](https://portal.azure.com)中，从虚拟机创建自定义映像时，可以选择具有通用映像或专用自定义映像。

- **专用自定义映像：** 未在计算机上运行 Sysprep/取消预配。 这意味着映像是现有虚拟机（快照）上的 OS 磁盘的精确副本。  当我们从这个自定义映像创建新计算机时，都存在相同的文件、应用程序、用户帐户、计算机名称等。
- **通用自定义映像：** 在计算机上运行了 Sysprep/取消预配。  此过程运行时，它会删除用户帐户、删除计算机名称、剥离用户注册表配置单元等，目的是对映像进行一般化，以便在创建另一个虚拟机时对其进行自定义。  当您对虚拟机进行通用化（通过运行 sysprep）时，该过程将销毁当前的虚拟机 - 它将不再正常工作。

在映像工厂中捕捉自定义映像的脚本将为在上一步中创建的任何虚拟机（根据 Azure 中资源上的标记标识）保存 VD。

## <a name="update-configuration-for-distributing-images"></a>用于分发映像的更新配置
该过程的下一步是将自定义映像从映像工厂实验室推送到任何其他需要它们的实验室。 此过程的核心部分是**实验室.json**配置文件。 您可以在映像工厂中包含的 **"配置"** 文件夹中找到此文件。

实验室.json 配置文件中列出了两个关键内容：

- 使用订阅 ID 和实验室名称唯一标识特定目标实验室。
- 应作为配置根的相对路径推送到实验室的特定图像集。 也可以指定整个文件夹（获取该文件夹中的所有图像）。

下面是一个示例实验室.json 文件，其中列出了两个实验室。 在这种情况下，您将图像分发到两个不同的实验室。

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>创建生成任务
使用本文前面看到的相同步骤，向生成定义添加其他 Azure **Powershell**生成任务。 填写下图所示的详细信息： 

![生成任务以分发图像](./media/save-distribute-custom-images/second-build-task-powershell.png)

参数为：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

此任务获取映像工厂中存在的任何自定义映像，并将它们推送到 Labs.json 文件中定义的任何实验室。

## <a name="queue-the-build"></a>对生成进行排队
分发生成任务完成后，将重新生成排队以确保一切正常工作。 生成成功完成后，新的自定义映像将显示在输入到 Labs.json 配置文件的目标实验室中。

## <a name="next-steps"></a>后续步骤
在本系列的下一篇文章中，您将使用保留策略和清理步骤更新映像工厂：[设置保留策略并运行清理脚本](image-factory-set-retention-policy-cleanup.md)。
