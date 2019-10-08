---
title: 使用 Azure 实验室服务设置 Linux shell 脚本编写实验室 |Microsoft Docs
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
ms.openlocfilehash: 69fe370029cef391b8ee483ffdee5c588df41d58
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030669"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>设置在 Linux 上讲授 shell 脚本的实验室
本文介绍如何设置实验室，以在 Linux 上讲授 shell 脚本。 脚本编写是系统管理的一个有用部分，可让管理员避免重复执行的任务。 在此示例方案中，类涵盖了传统的 bash 脚本和增强的脚本。 增强型脚本是结合 bash 命令和 Ruby 的脚本。 此方法允许 Ruby 传递数据和 bash 命令以与 shell 交互。 

采用这些脚本编写类的学生获取 Linux 虚拟机，以了解 Linux 的基本知识，并熟悉 bash shell 脚本。 Linux 虚拟机附带启用了远程桌面访问，并且安装了[gedit](https://help.gnome.org/users/gedit/stable/)和[Visual Studio Code](https://code.visualstudio.com/)文本编辑器。

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，需要一个 Azure 订阅才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 拥有 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户，也可以使用现有的实验室帐户。 请参阅以下教程，了解如何创建新的实验室帐户：[设置实验室帐户的教程](tutorial-setup-lab-account.md)。

创建实验室帐户后，在实验室帐户中启用以下设置： 

| 实验室帐户设置 | 说明 |
| ----------- | ------------ |  
| Marketplace 映像 | 启用[Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS)映像，以便在实验室帐户中使用。 有关详细信息，请参阅[指定可用于实验室创建者的 Marketplace 映像](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators) | 

按照[本教程](tutorial-setup-classroom-lab.md)创建新的实验室并应用以下设置：

| 实验室设置 | 值/说明 | 
| ------------ | ------------------ |
| 虚拟机（VM）大小 | 小  |
| VM 映像 | [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS) |
| 启用远程桌面连接 | 可. <p>如果启用此设置，教师和学生可以使用远程桌面（RDP）连接到其 Vm。 有关详细信息，请参阅在[Azure 实验室服务中的实验室中启用适用于 Linux 虚拟机的远程桌面](how-to-enable-remote-desktop-linux.md#teachers-connecting-to-the-template-vm-using-rdp)。 </p>|


## <a name="install-desktop-and-xrdp"></a>安装桌面和 xrdp
[Ubuntu server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS)映像默认情况下未安装远程桌面服务器。 按照[安装和配置远程桌面以连接到 Azure 中的 LINUX VM 一](../../virtual-machines/linux/use-remote-desktop.md)文中的说明，安装要通过远程桌面协议连接的模板计算机上所需的包。

## <a name="install-ruby"></a>安装 Ruby
Ruby 是一种开放源动态语言，可与 bash 脚本结合使用。 本部分演示如何使用 `apt-get` 来安装最新版本的[Ruby](https://www.ruby-lang.org/)。

1. 通过运行以下命令来安装更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安装[Ruby](https://www.ruby-lang.org/)。  Ruby 是一种开放源动态语言，可与 bash 脚本结合使用。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安装开发工具
本部分介绍如何安装几个文本编辑器。 Gedit 是 gnome 桌面环境的默认文本编辑器。 它设计为通用文本编辑器。 Visual Studio Code 是一种文本编辑器，其中包括对调试和源代码管理集成的支持。

> [!NOTE]
> 有几种不同的文本编辑器可用。 Visual Studio Code 和 gedit 只是两个示例。

1. 安装[gedit](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安装 [Visual Studio Code](https://code.visualstudio.com/)。  可以使用 Snap Store 安装 Visual Studio code。  有关备用安装选项，请参阅[Visual Studio Code 备用下载](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    该模板现已更新，并具有完成实验室所需的编程语言和开发工具。 现在可以将模板映像发布到实验室。 选择 "模板" 页上的 "**发布**" 按钮，将模板发布到实验室。  

## <a name="cost"></a>成本 
如果要估计此实验室的成本，可以使用以下示例：
 
对于具有20个小时的计划类时间和10小时配额（适用于家庭或分配）的25名学生，实验室的价格为： 

25名学生 * （20 + 10）小时 * 20 个实验室单位 * 0.01 美元/小时 = 150 美元

有关定价的详细信息，请参阅以下文档：[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文指导你完成为脚本编写类创建实验室的步骤。 尽管本文重点介绍了如何在 Linux 计算机上设置 Ruby 脚本编写工具，但相同的设置可用于其他脚本类，如 Linux 上的 Python。

## <a name="next-steps"></a>后续步骤
下一步是设置任何实验室的常见步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [向学生发送电子邮件注册链接](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link)。 





