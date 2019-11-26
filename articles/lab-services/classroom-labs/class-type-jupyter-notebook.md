---
title: 使用 Python 和 Jupyter 笔记本设置实验室来讲授数据科学 |Microsoft Docs
description: 了解如何使用 Python 和 Jupyter 笔记本设置实验室来讲授数据科学。
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
ms.openlocfilehash: b69abf098ba7646ebc98d126c7c0d949205d6275
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383977"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>使用 Python 和 Jupyter 笔记本设置实验室来讲授数据科学

本文概述了如何在实验室服务中设置模板计算机，以及如何使用[Jupyter 笔记本](http://jupyter-notebook.readthedocs.io)讲授学生所需的工具。  Jupyter 笔记本是一个开源项目，可让你轻松地将丰富的文本和可执行的[Python](https://www.python.org/)源代码组合到称为笔记本的单个画布上。  运行笔记本会生成输入和输出的线性记录。  这些输出可以包括文本、信息表、散点图等。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。  你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 有关如何启用 marketplace 映像的详细信息，请参阅[指定可用于实验室创建者的 marketplace 映像](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators)。

| 实验室帐户设置 | 说明 |
| ------------------- | ------------ |
| 市场映像 | 启用[Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映像以便在实验室帐户中使用。 |

>[!TIP]
>本文重点介绍如何配置使用 Windows Server 操作系统的模板计算机。  还可以使用[适用于 linux 的 Data Science Virtual Machine （CentOS）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) Data Science Virtual Machine 或 Azure Marketplace 中的[linux （Ubuntu）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)映像，使用 Python 和 Jupyter 笔记本设置数据科学类。

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 小型 GPU （计算）。 此大小最适用于计算密集型和网络密集型应用程序，如人工智能和深度学习。 |
|虚拟机映像| Windows Server 2019 上的 SQL Server 2019 标准版|

## <a name="template-machine"></a>模板计算机

[Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映像提供此类类型所需的深入学习框架和工具。  该映像包括 Jupyter 笔记本和 Visual Studio Code。  [Jupyter 笔记本](http://jupyter-notebook.readthedocs.io)是一个 web 应用程序，它允许数据科学家获取原始数据、运行计算，并查看相同环境中的所有结果。  对于模板计算机，web 应用程序将以本地方式运行。  [Visual Studio Code](https://code.visualstudio.com/)是在编写和测试笔记本时提供丰富交互式体验的 IDE。  有关详细信息，请参阅[在 Visual Studio Code 中使用 Jupyter 笔记本](https://code.visualstudio.com/docs/python/jupyter-support)。

用于设置类的其余任务是提供本地笔记本。  有关如何使用 Azure 机器学习示例的说明，请参阅[如何使用 Jupyter 笔记本配置环境](../../machine-learning/service/how-to-configure-environment.md#jupyter)。  你还可以在模板计算机上提供自己的笔记本。  发布模板时，笔记本将复制到所有学生计算机。

## <a name="cost-estimate"></a>成本估计

我们来介绍此类的可能的成本估算。  我们将使用一类25名学生。  计划的类时间有20小时。  此外，每个学生在计划的类时间之外为家庭作业或分配获取10小时配额。  我们选择的虚拟机大小为小 gpu （计算），即139实验室单位。

下面是此类的可能的成本估算示例：

25名学生 \* （20个计划小时 + 10 个配额小时） \* 139 实验室单位 \* 0.01 美元/小时 = 1042.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文逐步介绍了为 Jupyter 笔记本类创建实验室的步骤。 对于其他机器学习类，您可以使用类似的设置。

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
