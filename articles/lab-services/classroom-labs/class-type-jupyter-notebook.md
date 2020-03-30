---
title: 设置实验室，使用 Python 和 Jupyter 笔记本教授数据科学 |微软文档
description: 了解如何设置实验室，使用 Python 和 Jupyter 笔记本教授数据科学。
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444105"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>设置实验室，使用 Python 和 Jupyter 笔记本教授数据科学

本文概述了如何在实验室服务中设置模板计算机，并配备教学生如何使用[Jupyter 笔记本](http://jupyter-notebook.readthedocs.io)所需的工具。  Jupyter Notebooks 是一个开源项目，可让你轻松地在名为“笔记本”的单张画布上组合使用丰富的文本和可执行的 [Python](https://www.python.org/) 源代码。  运行笔记本会生成输入和输出的线性记录。  这些输出可能包括文本、信息表、散点图等。

## <a name="lab-configuration"></a>实验室配置

要设置此实验，需要 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户的教程](tutorial-setup-lab-account.md)。  您还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中描述的设置。 有关如何启用市场映像的详细信息，请参阅[指定实验室创建者可用的应用商店映像](specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
| 市场映像 | 启用[数据科学虚拟机 - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映像，以便在实验室帐户中使用。 |

>[!TIP]
>本文将重点介绍使用 Windows Server 操作系统的模板计算机。  还可以使用[数据科学虚拟机为 Linux （CentOS）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm)或数据科学虚拟机为 Azure 应用商店中的[Linux （Ubuntu）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)映像设置数据科学类。

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置教室实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 小型 GPU（计算）。 此大小最适合计算密集型和网络密集型应用，如人工智能和深度学习。 |
|虚拟机映像| 数据科学虚拟机 - Windows 2016|

## <a name="template-machine"></a>模板机

[数据科学虚拟机 - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映像提供了此类课程所需的必要深度学习框架和工具。  该图像包括犹太笔记本和视觉工作室代码。  [Jupyter 笔记本](http://jupyter-notebook.readthedocs.io)是一个 Web 应用程序，它允许数据科学家获取原始数据、运行计算并在相同的环境中查看结果。  对于我们的模板计算机，Web 应用程序将在本地运行。  [Visual Studio Code](https://code.visualstudio.com/)是一种 IDE，在编写和测试笔记本时提供了丰富的交互式体验。  有关详细信息，请参阅[在可视化工作室代码中使用 Jupyter 笔记本](https://code.visualstudio.com/docs/python/jupyter-support)。

设置类的剩余任务是提供本地笔记本。  有关如何使用 Azure 机器学习示例的说明，请参阅[如何使用 Jupyter 笔记本配置环境](../../machine-learning/how-to-configure-environment.md#jupyter)。  您还可以在模板计算机上提供您自己的笔记本。  发布模板时，笔记本将复制到所有学生计算机。

## <a name="cost-estimate"></a>成本估算

让我们来介绍此类的可能成本估算。  我们将使用一个25名学生的班级。  有 20 小时的计划上课时间。  此外，每个学生在计划上课时间之外的家庭作业或作业将获得 10 小时的配额。  我们选择的虚拟机大小是小型 GPU（计算），即 139 个实验室单元。

下面是此类的可能成本估算的示例：

25\*名学生（20 个预定小时 = \* 10 个配额\*小时） 139 个实验室单位每小时 0.01 USD = 1042.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

在本文中，我们介绍了为 Jupyter 笔记本类创建实验室的步骤。 您可以将类似的设置用于其他机器学习类。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)
