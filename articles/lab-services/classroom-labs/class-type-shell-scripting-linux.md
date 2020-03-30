---
title: 使用 Azure 实验室服务设置 Linux 外壳脚本实验室 |微软文档
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
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443544"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>设置一个实验室来教授 Linux 上的 shell 脚本
本文介绍如何设置一个实验室来在 Linux 上教授 shell 脚本。 脚本编写是系统管理的有用组成部分，可让管理员避免重复性的任务。 在此示例场景中，类涵盖了传统的 bash 脚本和增强的脚本。 增强的脚本是结合了 bash 命令和 Ruby 的脚本。 这样，Ruby 便可以传递数据和 bash 命令来与 shell 交互。 

使用这些脚本类的学生可以通过 Linux 虚拟机了解 Linux 的基础知识，并熟悉 bash shell 脚本。 该 Linux 虚拟机已启用远程桌面访问，并装有 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文本编辑器。

## <a name="lab-configuration"></a>实验室配置
要设置此实验，需要 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获得 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 有关创建新实验室帐户，请参阅以下教程：[设置实验室帐户的教程](tutorial-setup-lab-account.md)。

创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | Instructions |
| ----------- | ------------ |  
| 市场映像 | 启用[Ubuntu 服务器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映像，以便在实验室帐户中使用。 有关详细信息，请参阅[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。 | 

请按照[本教程](tutorial-setup-classroom-lab.md)创建新实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机 （VM） 大小 | 小型  |
| VM 映像 | [乌本图服务器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| 启用远程桌面连接 | 启用。 <p>启用此设置将允许教师和学生使用远程桌面 （RDP） 连接到其 VM。 有关详细信息，请参阅在[Azure 实验室服务 中的实验室中为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)。 </p>|


## <a name="install-desktop-and-xrdp"></a>安装桌面和 xrdp
[默认情况下，Ubuntu 服务器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映像未安装远程桌面服务器。 按照["安装"中的说明进行配置远程桌面以连接到 Azure 文章中的 Linux VM，](../../virtual-machines/linux/use-remote-desktop.md)以安装模板计算机上通过远程桌面协议进行连接所需的包。

## <a name="install-ruby"></a>安装 Ruby
Ruby 是一种开源动态语言，可与 bash 脚本结合使用。 本节演示如何使用来`apt-get`安装最新版本的[Ruby](https://www.ruby-lang.org/)。

1. 通过运行以下命令安装更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安装[红宝石](https://www.ruby-lang.org/)。  Ruby 是一种开源动态语言，可与 bash 脚本结合使用。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安装开发工具
本节介绍如何安装几个文本编辑器。 Gedit 是 gnome 桌面环境的默认文本编辑器。 它设计为通用文本编辑器。 Visual Studio Code 是一个文本编辑器，包括对调试和源代码管理集成的支持。

> [!NOTE]
> 有几个不同的文本编辑器可用。 视觉工作室代码和 gedit 只是两个示例。

1. 安装[格迪特](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安装[可视化工作室代码](https://code.visualstudio.com/)。  可以使用快照存储安装可视化工作室代码。  有关备用安装选项，请参阅[可视化工作室代码备用下载](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    模板现已更新，并具有完成实验所需的编程语言和开发工具。 模板映像现在可以发布到实验室。 选择模板页上的 **"发布"** 按钮，将模板发布到实验室。  

## <a name="cost"></a>成本 
如果要估计本实验的成本，可以使用以下示例：
 
对于有 25 名学生的班级，有 20 小时的上课时间和 10 小时的家庭作业或作业配额，实验室的价格是： 

25 名学生 = （20 + 10） 小时 = 20 个实验室单位 = 每小时 0.01 USD = 150 USD

有关定价的详细信息，请参阅以下文档[：Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文介绍了为脚本类创建实验室的步骤。 虽然本文侧重于在Linux计算机上设置Ruby脚本工具，但相同的设置可用于其他脚本类，如Linux上的Python。

## <a name="next-steps"></a>后续步骤
设置任何实验室通常采取以下步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)。 





