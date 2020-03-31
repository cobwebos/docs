---
title: 使用 Azure 实验室服务设置侧重于深度学习的实验室 |微软文档
description: 了解如何设置实验室在 Linux 上教授 shell 脚本。
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109389"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 实验室服务设置专注于自然语言处理中的深度学习的实验室
本文介绍如何使用 Azure 实验室服务设置侧重于自然语言处理 （NLP） 深度学习的实验室。 自然语言处理 (NLP) 是某种形式的人工智能 (AI)，可在计算机中实现翻译、语音识别和其他语言理解功能。  

使用 NLP 类的学生可以通过 Linux 虚拟机 (VM) 了解如何应用神经网络算法，以开发深度学习模型用于分析人类手写语言。 

## <a name="lab-configuration"></a>实验室配置
要设置此实验，需要 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获得 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 有关创建新实验室帐户，请参阅以下教程：[设置实验室帐户的教程](tutorial-setup-lab-account.md)。
 
创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | Instructions |
| ----------- | ------------ |  
| 市场映像 | 启用适用于 Linux （Ubuntu） 映像的数据科学虚拟机，以便在实验室帐户中使用。  有关说明，请参阅以下文章：[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。 | 

请按照[本教程](tutorial-setup-classroom-lab.md)创建新实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机 （VM） 大小 | 小型 GPU（计算）。 此大小最适合计算密集型和网络密集型应用，如人工智能和深度学习。 |
| VM 映像 | [用于 Linux （Ubuntu） 的数据科学虚拟机](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 此图像为机器学习和数据科学提供了深度学习框架和工具。 要查看此映像上已安装工具的完整列表，请参阅以下文章[：DSVM 上包含哪些内容？](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| 启用远程桌面连接 | 启用。 <p>启用此设置将允许教师和学生使用远程桌面 （RDP） 连接到虚拟机 （VM）。</p><p>**重要提示**：RDP 已在用于 Linux 映像的数据科学虚拟机上安装和配置。 因此，教师/学生可以通过 RDP 连接到 VM，而无需执行任何其他步骤。 此外，如果您需要连接到图形桌面，此映像已在虚拟机上安装[了 X2Go 服务器](https://wiki.x2go.org/doku.php/doc:newtox2go)。 学生必须在其本地计算机上安装 X2Go 客户端，并且必须使用客户端进行连接。 有关详细信息，请参阅以下指南： <ul><li>[如何访问适用于 Linux 的数据科学虚拟机](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[连接到模板 VM 以安装 RDP 和 GUI 包](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

用于 Linux 映像的数据科学虚拟机提供了此类课程所需的必要深度学习框架和工具。 因此，在模板计算机创建后，无需进一步自定义它。 它可以发布供学生使用。 选择模板页上的 **"发布"** 按钮，将模板发布到实验室。  

## <a name="cost"></a>成本
如果要估计本实验的成本，可以使用以下示例： 

对于有 25 小时的预定上课时间和 10 小时作业或作业配额的班级，实验室的价格为 - 25 名学生 = （20 + 10） 小时 = 139 实验室单位 = 每小时 0.01 USD = 1042.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文将引导您完成为自然语言处理类创建实验室的步骤。 您可以将类似的设置用于其他深度学习课程。

## <a name="next-steps"></a>后续步骤
设置任何实验室通常采取以下步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)。 

