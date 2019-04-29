---
title: 保存并将在 Azure 开发测试实验室中的映像分发 |Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622592"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>保存自定义映像并分发到多个实验室
本文介绍可将已创建的虚拟机 (Vm) 中保存自定义映像的步骤。 它还介绍了如何进行这些自定义将映像分发到组织中其他开发测试实验室。

## <a name="prerequisites"></a>必备组件
以下各项应已就位：

- 在 Azure 开发测试实验室中的图像工厂实验室。
- Azure DevOps 项目用于自动执行映像工厂。
- 源代码位置包含脚本和配置 （在本示例中，在同一 DevOps 项目在上一步中所述）。
- 生成定义来安排 Azure Powershell 任务。

如果需要请按照步骤[从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)若要创建或设置这些项。 

## <a name="save-vms-as-generalized-vhds"></a>将 Vm 保存为通用化 Vhd
将现有的 Vm 保存为通用化 Vhd。  没有要将现有的 Vm 保存为通用化 Vhd 的示例 PowerShell 脚本。 若要使用它，首先，添加另一个**Azure Powershell**任务到生成定义，如在下图中所示：

![添加 Azure PowerShell 步骤](./media/save-distribute-custom-images/powershell-step.png)

后新的任务列表中，选择的项，因此我们可以填写所有详细信息，如在下图中所示： 

![PowerShell 设置](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>与专用的自定义映像通用化
在中[Azure 门户](https://portal.azure.com)，当从虚拟机创建自定义映像，你可以选择允许的通用或专用的自定义映像。

- **专用的自定义映像：** 没有在计算机上运行 Sysprep/取消设置。 这意味着该图像是现有虚拟机 (snapshot）) 上的 OS 磁盘的精确副本。  相同的文件、 应用程序、 用户帐户、 计算机名称和等等，都具备我们从此自定义映像创建新计算机时。
- **通用自定义映像：** Sysprep/取消设置已在计算机上运行。  此过程运行时，它将删除用户帐户、 删除计算机名称，剥离用户注册表配置单元，等等，目标是通用化映像，因此它可以进行自定义时创建另一个虚拟机。  时 （通过运行 sysprep） 通用化虚拟机，该过程销毁当前虚拟机 – 它将不再能正常运行。

对齐图像工厂中的自定义映像的脚本将保存为在上一步中创建任何虚拟机的 Vhd (标识基于在 Azure 中的资源标记)。

## <a name="update-configuration-for-distributing-images"></a>更新分发映像的配置
该过程的下一步是从出图像工厂实验室的自定义映像推送到需要它们的任何其他实验室。 此过程的核心部分是**labs.json**配置文件。 您可以找到此文件中的**配置**图像工厂中包含的文件夹。

有两个 labs.json 配置文件中列出的重要事项：

- 唯一标识特定的目标实验室中使用的订阅 ID 和实验室名称。
- 应为相对配置根路径到本实验推送的映像特定的一组。 可以指定整个文件夹 （若要在该文件夹中获取所有映像） 过。

下面是通过列出的两个实验室的示例 labs.json 文件。 在这种情况下，将分发到两个不同的实验室的映像。

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
使用你学习了本文前面部分的相同步骤添加其他**Azure Powershell**给您的生成任务生成定义。 填写详细信息，如在下图中所示： 

![生成将映像分发的任务](./media/save-distribute-custom-images/second-build-task-powershell.png)

参数包括： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

此任务将图像工厂中存在的任何自定义映像，并将它们推送到 Labs.json 文件中定义所有实验室。

## <a name="queue-the-build"></a>对生成进行排队
分发生成任务完成后，以确保一切正常对新生成进行排队。 生成成功完成后，新的自定义映像将显示已输入到 Labs.json 配置文件在目标实验室中。

## <a name="next-steps"></a>后续步骤
在该系列的下一篇文章，与保留策略和清理步骤更新映像工厂：[运行清理脚本并设置保留策略](image-factory-set-retention-policy-cleanup.md)。
