---
title: 使用 Azure 实验室服务设置专注于深度学习的实验室 | Microsoft Docs
description: 了解如何设置一个实验室来讲解 Linux 上的 shell 脚本编写。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 1167846c399430bd2db2eaa3114628ebb63ce639
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592315"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 实验室服务设置专注于自然语言处理中的深度学习的实验室
本文介绍如何使用 Azure 实验室服务来设置一个专注于自然语言处理 (NLP) 中的深度学习的实验室。 自然语言处理 (NLP) 是某种形式的人工智能 (AI)，可在计算机中实现翻译、语音识别和其他语言理解功能。  

使用 NLP 类的学生可以通过 Linux 虚拟机 (VM) 了解如何应用神经网络算法，以开发深度学习模型用于分析人类手写语言。 

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，你需要具有一个 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 拥有 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 请参阅以下教程，了解如何创建新的实验室帐户：[有关设置实验室帐户的教程](tutorial-setup-lab-account.md)。
 
创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | Instructions |
| ----------- | ------------ |  
| 市场映像 | 启用用于 Linux (Ubuntu) 的 Data Science Virtual Machine 映像，以便在实验室帐户中使用。  有关说明，请参阅以下文章：[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。 | 

按照[此教程](tutorial-setup-classroom-lab.md)创建新的实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机 (VM) 大小 | 小型 GPU (计算)。 此大小最适用于计算密集型和网络密集型应用程序（如人工智能和深度学习）。 |
| VM 映像 | [用于 Linux (Ubuntu)的 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。 此映像提供机器学习和数据科学深度学习框架和工具。 若要查看此映像上安装的工具的完整列表，请参阅以下文章：[DSVM 中包含哪些组件？](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)。 |
| 启用远程桌面连接 | <p>如果启用此设置，教师和学生就可以使用远程桌面 (RDP) 连接到虚拟机 (VM)。</p><p>**重要说明**：启用此设置只会打开 Linux 计算机上的 RDP 端口。 如果已在虚拟机映像上安装并配置了 RDP，则你/学生可以通过 RDP 连接到 VM，而无需执行任何其他步骤。 <p>如果 VM 映像未安装和配置 RDP，则你首次需要使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便你/学生以后可以使用 RDP 连接到 Linux 计算机。 有关详细信息，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)。 然后发布该映像，以便学生可以通过 RDP 登录到学生 Linux VM。  |

用于 Linux 的 Data Science Virtual Machine 映像提供此类型课程所需的深度学习框架和工具。 因此，在创建模板虚拟机后，无需进一步自定义。 可以将其发布供学生使用。 选择模板页上的“发布”按钮，将模板发布到实验室。  

## <a name="cost"></a>成本
如果要估算此实验室的成本，可以使用以下示例： 

如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或任务时数配额为 10 小时，则实验室的价格为 - 25 个学生 * (20 + 10) 个小时 * 139 个实验室单位 * 0.01 美元/小时 = 1042.5 美元

有关定价的更多详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文已指导你完成为自然语言处理课程创建实验室的步骤。 对于其他深度学习课程，可以使用类似的设置。

## <a name="next-steps"></a>后续步骤
接下来的步骤是设置任何实验室时通用的：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)。 

