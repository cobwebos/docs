---
title: 在 Azure 开发测试实验室中保存和分发图像 |Microsoft Docs
description: 本文介绍了如何在 Azure 开发测试实验室中从已创建的虚拟机 (Vm) 保存自定义映像。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476234"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>保存自定义映像并分发到多个实验室
本文提供了一些步骤，用于从已创建的虚拟机 (Vm) 保存自定义映像。 还介绍了如何将这些自定义映像分发到组织中的其他开发测试实验室。

## <a name="prerequisites"></a>先决条件
应已准备好以下各项：

- Azure 开发测试实验室中的映像工厂的实验室。
- 用于自动执行映像工厂的 Azure DevOps 项目。
- 包含脚本和配置 (的源代码位置，在我们的示例中，) 上一步中提到的相同 DevOps 项目中。
- 用于协调 Azure Powershell 任务的生成定义。

如果需要，请按照 [从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md) 中的步骤创建或设置这些项。 

## <a name="save-vms-as-generalized-vhds"></a>将 Vm 保存为通用 Vhd
将现有 Vm 保存为通用 Vhd。  有一个用于将现有 Vm 保存为通用 Vhd 的示例 PowerShell 脚本。 若要使用它，请先向生成定义添加另一个 **Azure Powershell** 任务，如下图所示：

![添加 Azure PowerShell 步骤](./media/save-distribute-custom-images/powershell-step.png)

在列表中具有新任务后，请选择该项目，以便我们可以填写所有详细信息，如下图所示： 

![PowerShell 设置](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>通用化与专用自定义映像
在 [Azure 门户](https://portal.azure.com)中，当从虚拟机创建自定义映像时，可以选择使用通用化或专用自定义映像。

- **专用自定义映像：** 未在计算机上运行 Sysprep/取消设置。 这意味着映像是现有虚拟机上操作系统磁盘的精确副本， (快照) 。  从此自定义映像创建新的计算机时，相同的文件、应用程序、用户帐户、计算机名等都是存在的。
- **通用自定义映像：** 在计算机上运行了 Sysprep/取消设置。  此进程运行时，它会删除用户帐户、删除计算机名称、删除用户注册表配置单元等，并在创建另一台虚拟机时对其进行自定义。  通过运行 sysprep) 通用化虚拟机 (，该进程会销毁当前虚拟机–它将不再正常工作。

用于在映像工厂中对齐自定义映像的脚本将保存在之前步骤中创建的任何虚拟机的 Vhd (根据 Azure) 中资源上的标记进行标识。

## <a name="update-configuration-for-distributing-images"></a>更新分发映像的配置
此过程中的下一步是将自定义映像从映像工厂实验室推送到需要它们的任何其他实验室。 此过程的核心部分是配置文件的 **labs.js** 。 可在映像工厂中包含的 **配置** 文件夹中找到此文件。

配置文件的 labs.js中列出了两个关键内容：

- 使用订阅 ID 和实验室名称唯一地标识特定目标实验室。
- 应作为配置根的相对路径推送到实验室的一组特定图像。 您可以指定整个文件夹 (以便还) 该文件夹中的所有图像。

下面是一个示例，其中列出了列出了两个实验的文件 labs.js。 在这种情况下，需要将映像分发到两个不同的实验室。

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
使用在本文前面所述的相同步骤，将额外的 **Azure Powershell** 生成任务添加到生成定义。 填写详细信息，如下图所示： 

![生成任务以分发图像](./media/save-distribute-custom-images/second-build-task-powershell.png)

参数包括： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

此任务使用映像工厂中的任何自定义映像，并将它们推送到在文件 Labs.js上定义的任何实验室。

## <a name="queue-the-build"></a>对生成进行排队
分发生成任务完成后，排队新的生成以确保一切正常。 成功完成生成后，会在目标实验室中显示新的自定义映像，并将其输入到 Labs.js配置文件中。

## <a name="next-steps"></a>后续步骤
在本系列的下一篇文章中，你将使用保留策略和清理步骤更新映像工厂： [设置保留策略和运行清理脚本](image-factory-set-retention-policy-cleanup.md)。
