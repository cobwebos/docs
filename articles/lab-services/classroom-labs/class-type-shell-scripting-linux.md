---
title: 使用 Azure 实验室服务设置 Linux shell 脚本编写实验室 | Microsoft Docs
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 66b325eb1d268fdd5b1052a0da84c603186edf65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589493"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>设置一个实验室来讲解 Linux 上的 shell 脚本编写
本文展示了如何设置一个实验室来讲解 Linux 上的 shell 脚本编写。 脚本编写是系统管理的有用组成部分，可让管理员避免重复性的任务。 在此示例场景中，类涵盖了传统的 bash 脚本和增强的脚本。 增强的脚本是结合了 bash 命令和 Ruby 的脚本。 这样，Ruby 便可以传递数据和 bash 命令来与 shell 交互。 

使用这些脚本类的学生可以通过 Linux 虚拟机了解 Linux 的基础知识，并熟悉 bash shell 脚本。 该 Linux 虚拟机已启用远程桌面访问，并装有 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文本编辑器。

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，你需要有一个 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 拥有 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 请参阅以下教程，了解如何创建新的实验室帐户：[有关设置实验室帐户的教程](tutorial-setup-lab-account.md)。

创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | Instructions |
| ----------- | ------------ |  
| 市场映像 | 启用 Ubuntu Server 18.04 LTS 映像，以便在实验室帐户中使用。 有关详细信息，请参阅[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。 | 

按照[此教程](tutorial-setup-classroom-lab.md)创建新的实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机 (VM) 大小 | 小型  |
| VM 映像 | Ubuntu Server 18.04 LTS |
| 启用远程桌面连接 | 启用。 <p>如果启用此设置，教师和学生将可以使用远程桌面 (RDP) 连接到其 VM。 有关详细信息，请参阅[在 Azure 实验室服务的实验室中为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)。 </p>|


## <a name="install-desktop-and-xrdp"></a>安装桌面和 xrdp
Ubuntu Server 18.04 LTS 映像默认情况下不安装远程桌面服务器。 按照[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)一文中的说明，在模板计算机上安装通过远程桌面协议进行连接所需的包。

## <a name="install-ruby"></a>安装 Ruby
Ruby 是一种开源动态语言，可与 bash 脚本结合使用。 本部分展示了如何使用 `apt-get` 来安装最新版本的 [Ruby](https://www.ruby-lang.org/)。

1. 运行以下命令来安装更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安装 [Ruby](https://www.ruby-lang.org/)。  Ruby 是一种开源动态语言，可与 bash 脚本结合使用。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安装开发工具
本部分介绍如何安装几个文本编辑器。 Gedit 是 gnome 桌面环境的默认文本编辑器。 它设计为通用文本编辑器。 Visual Studio Code 是一个支持调试和源代码管理集成的文本编辑器。

> [!NOTE]
> 有多个不同的文本编辑器可用。 Visual Studio Code 和 gedit 只是两个示例。

1. 安装 [gedit](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安装 [Visual Studio Code](https://code.visualstudio.com/)。  可以使用 Snap Store 安装 Visual Studio Code。  有关备用安装选项，请参阅 [Visual Studio Code 备用下载](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    该模板现已更新，并具有完成实验室所需的编程语言和开发工具。 现在可以将模板映像发布到实验室。 选择模板页上的“发布”按钮，将模板发布到实验室。  

## <a name="cost"></a>成本 
如果要估计此实验室的成本，可以使用以下示例：
 
如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或作业时数配额为 10 小时，则实验室的价格为： 

25 个学生 * (20 + 10) 小时 * 20 实验室单位 * 0.01 美元/小时 = 150 美元

有关定价的详细信息，可参阅以下文档：[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文指导你完成了为脚本编写课程创建实验室的步骤。 尽管本文重点介绍了如何在 Linux 计算机上设置 Ruby 脚本编写工具，但相同的设置可用于其他脚本编写课程，例如“Linux 上的 Python”。

## <a name="next-steps"></a>后续步骤
后续步骤是设置任何实验室时通用的：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)。 





