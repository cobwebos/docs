---
title: 使用 Azure 实验室服务将实验室设置为深度学习 |Microsoft Docs
description: 了解如何设置在 Linux 上讲授 shell 脚本的实验室。
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
ms.openlocfilehash: 0aa30c114153521258842fc5f1e9150053b766f4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332303"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 实验室服务在自然语言处理中设置一个以深度学习为重点的实验室
本文介绍如何使用 Azure 实验室服务，设置以自然语言处理（NLP）为深度学习的实验室。 自然语言处理 (NLP) 是某种形式的人工智能 (AI)，可在计算机中实现翻译、语音识别和其他语言理解功能。  

使用 NLP 类的学生可以通过 Linux 虚拟机 (VM) 了解如何应用神经网络算法，以开发深度学习模型用于分析人类手写语言。 

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，需要一个 Azure 订阅才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 拥有 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 请参阅以下教程，了解如何创建新的实验室帐户：[设置实验室帐户教程](tutorial-setup-lab-account.md)。
 
创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | 说明 |
| ----------- | ------------ |  
| Marketplace 映像 | 启用适用于 Linux （Ubuntu）映像的 Data Science Virtual Machine，以便在实验室帐户中使用。  有关说明，请参阅以下文章：[指定可用于实验室创建者的 marketplace 映像](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators)。 | 

按照[本教程](tutorial-setup-classroom-lab.md)创建新的实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机（VM）大小 | 小型 GPU （计算）。 此大小最适用于计算密集型和网络密集型应用程序，如人工智能和深度学习。 |
| VM 映像 | [适用于 Linux （Ubuntu）的 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 此图提供了用于机器学习和数据科学的深度学习框架和工具。 若要查看此映像上安装的工具的完整列表，请参阅以下文章： [DSVM 上包含的内容？](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)。 |
| 启用远程桌面连接 | 可. <p>如果启用此设置，教师和学生可以使用远程桌面（RDP）连接到他们的虚拟机（VM）。</p><p>**重要提示**：已在 Linux 映像的 Data Science Virtual Machine 上安装并配置了 RDP。 因此，教师/学生可以通过 RDP 连接到 Vm，而无需执行任何其他步骤。 此外，如果需要连接到图形桌面，此映像已经在虚拟机上安装了[X2Go 服务器](https://wiki.x2go.org/doku.php/doc:newtox2go)。 学生必须在本地计算机上安装 X2Go 客户端，并且必须使用客户端进行连接。 有关详细信息，请参阅以下指南： <ul><li>[如何访问适用于 Linux 的 Data Science Virtual Machine](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[连接到模板 VM 以安装 RDP 和 GUI 包](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

适用于 Linux 映像的 Data Science Virtual Machine 提供此类类型所需的深入学习框架和工具。 因此，在创建模板计算机之后，无需进一步自定义。 可以将其发布以供学生使用。 选择 "模板" 页上的 "**发布**" 按钮，将模板发布到实验室。  

## <a name="cost"></a>费用
如果要估计此实验室的成本，可以使用以下示例： 

对于具有20个小时的计划类时间和10小时配额（适用于家庭作业或分配）的25名学生，实验室价格为-25 个学生 * （20 + 10）小时 * 139 实验室单位 * 0.01 美元/小时 = 1042.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文指导你完成为自然语言处理类创建实验室的步骤。 对于其他深度学习类，您可以使用类似的设置。

## <a name="next-steps"></a>后续步骤
下一步是设置任何实验室的常见步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)。 

