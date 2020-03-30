---
title: 使用 Azure 实验室服务设置道德黑客实验室 |微软文档
description: 了解如何使用 Azure 实验室服务设置实验室来教授道德黑客。
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133176"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>设置实验室教授道德黑客课程 
本文介绍如何设置一个侧重于道德黑客的取证方面的类。 渗透测试是道德黑客社区使用的一种做法，当某人试图获得对系统或网络的访问权限以证明恶意攻击者可能利用的漏洞时，就会进行渗透测试。 

在道德黑客课程中，学生可以学习抵御漏洞的新式技术。 每个学生都获得一个 Windows Server 主机虚拟机，它包含两个嵌套虚拟机 - 一个是带有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像的虚拟机，另一个是带有 [Kali Linux](https://www.kali.org/) 映像的虚拟机。 Metasploitable 虚拟机用于开发目的，Kali 虚拟机提供对执行取证任务所需的工具的访问。

本文有两个主要部分。 第一部分介绍如何创建课堂实验室。 第二部分介绍如何在启用嵌套虚拟化以及使用所需的工具和映像创建模板计算机。 在这种情况下，在启用 Hyper-V 以承载映像的计算机上，计算机可处理映像和 Kali Linux 映像。

## <a name="lab-configuration"></a>实验室配置
要设置此实验，需要 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户或使用现有帐户。 有关创建新实验室帐户，请参阅以下教程：[设置实验室帐户的教程](tutorial-setup-lab-account.md)。

按照[本教程](tutorial-setup-classroom-lab.md)创建新实验室，然后应用以下设置：

| 虚拟机大小 | 图像 |
| -------------------- | ----- | 
| 中等（嵌套虚拟化） | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>模板机 

创建模板计算机后，启动计算机并连接到它以完成以下三个主要任务。 
 
1. 为嵌套虚拟化设置计算机。 它支持所有适当的窗口功能，如 Hyper-V，并设置网络，使 Hyper-V 图像能够相互通信和互联网。
2. 设置[卡利](https://www.kali.org/)Linux 映像。 Kali 是一个 Linux 发行版，包括用于渗透测试和安全审核的工具。
3. 设置可工作图像。 在此示例中，将使用[Metasploit3](https://github.com/rapid7/metasploitable3)图像。 创建此映像的目的是故意存在安全漏洞。

[实验室服务道德黑客脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)提供了自动执行上述任务的脚本。

### <a name="prepare-template-machine-for-nested-virtualization"></a>为嵌套虚拟化准备模板计算机
请按照[本文](how-to-enable-nested-virtualization-template-vm.md)中的说明为嵌套虚拟化准备模板虚拟机。 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用卡利 Linux 映像设置嵌套虚拟机
Kali 是一个 Linux 发行版，包括用于渗透测试和安全审核的工具。

1. 从[https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下载图像。  
    1. 下载**Kali Linux Hyper-V 64 位**，用于超 V。
    1. 提取 .7z 文件。  如果您还没有 7 个 zip，请从[https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)下载。 记住提取的文件夹的位置，因为稍后需要它。
2. 从管理工具打开**超 V 管理器**。
1. 选择**操作**，然后选择 **"导入虚拟机**"。 
1. 在 **"导入虚拟机"** 向导的 **"查找文件夹**"页上，选择保存 Kali Linux 映像的提取文件夹的位置。

    ![查找文件夹对话框](../media/class-type-ethical-hacking/locate-folder.png)
1. 在 **"选择虚拟机"** 页上，选择卡利 Linux 映像。  在这种情况下，图像是**kali-linux-2019.3-hyperv**。

    ![选择卡利图像](../media/class-type-ethical-hacking/select-kali-image.png)
1.  在 **"选择导入类型"** 页上，选择 **"复制虚拟机"（创建新的唯一 ID）。**

    ![选择导入类型](../media/class-type-ethical-hacking/choose-import-type.png)
1. 接受"**为虚拟机文件选择文件夹"** 的默认设置，**然后选择"文件夹以存储虚拟硬盘"** 页，然后选择 **"下一步**"。
1. 在 **"连接网络"** 页上，选择本文的"**准备模板"** 部分中较早创建的**LabServicesSwitch，** 然后选择 **"下一步**"。

    ![连接网络页面](../media/class-type-ethical-hacking/connect-network.png)
1. 在 **"摘要"** 页上选择 **"完成**"。 等待，直到复制和导入操作完成。 Kali Linux 虚拟机现在将在 Hyper-V 中提供。
1. 从**Hyper-V 管理器**中，选择 **"操作** -> **开始"，** 然后选择 **"操作** -> **连接**"以连接到虚拟机。  
12. 默认用户名是 `root`，密码是 `toor`。 

    > [!NOTE]
    > 如果需要解锁图像，请按 CTRL 键向上拖动鼠标。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>使用可分层映像设置嵌套 VM  
Rapid7 可美用图像是专门配置安全漏洞的图像。 您将使用此图像来测试和查找问题。 以下说明演示如何使用预先创建的 Metasploit 图像。 但是，如果需要较新版本的 Metasploit 图像，请参阅[https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)。

1. 导航到[https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填写表单以下载图像并选择"**提交**"按钮。
1. 选择"**立即下载可使用"** 按钮。
1. 下载 zip 文件时，提取 zip 文件并记住位置。
1. 将提取的 vmdk 文件转换为 vhdx 文件，以便可以使用 Hyper-V。 为此，请打开具有管理权限的 PowerShell 并导航到 vmdk 文件所在的文件夹，并按照以下说明操作：
    1. 下载[微软虚拟机转换器](https://www.microsoft.com/download/details.aspx?id=42497)，并在提示时运行mvmc_setup.msi文件。
    1. 导入 PowerShell 模块。  安装模块的默认位置是 C：[程序文件]微软虚拟机转换器*

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. 将 vmdk 转换为 Vhd 文件，该文件可用于 Hyper-V。 此操作可能需要几分钟。
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. 将新创建的美用.vhdx 复制到 C：\用户\公共\文档\Hyper-V\虚拟硬盘*。 
1. 创建新的超 V 虚拟机。
    1. 打开**超 V 管理器**。
    1. 选择**操作** -> **新** -> **虚拟机**。
    1. 在 **"开始之前**"新**虚拟机向导**的页面上，单击 **"下一步**"。
    1. 在 **"指定名称和位置**"页上，输入**名称**的 **"可使用**"，然后选择 **"下一步**"。

        ![新的 VM 映像向导](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 **"指定生成"** 页上，接受默认值，然后选择 **"下一步**"。
    1. 在 **"分配内存"** 页上，为**启动内存**输入**512 MB，** 然后选择 **"下一步**"。 

        ![“分配内存”页](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 **"配置网络"** 页上，将连接保留为 **"未连接**"。 稍后将设置网络适配器。
    1. 在 **"连接虚拟硬盘"** 页上，选择 **"使用现有虚拟硬盘**"。 浏览到上一步中创建的**美用.vhdx**文件的位置，然后选择 **"下一步**"。 

        ![连接虚拟网络磁盘页面](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 **"完成新虚拟机向导"** 页上，然后选择 **"完成**"。
    1. 创建虚拟机后，请在 Hyper-V 管理器中选择它。 还别打开机器。  
    1. 选择**操作** -> **设置**。
    1. 在"可使用 **"对话框的"设置**"上，选择 **"添加硬件**"。 
    1. 选择**旧网络适配器**，然后选择 **"添加**"。

        ![网络适配器页面](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 **"旧版网络适配器**"页上，为**虚拟交换机**设置选择**LabServicesSwitch，** 然后选择 **"确定**"。 LabServicesSwitch 是在"**准备嵌套虚拟化模板"** 部分中为 Hyper-V 准备模板时创建的。

        ![旧网络适配器页面](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. 可使用图像现已准备就绪。 从**Hyper-V 管理器**中，选择 **"操作** -> **开始"，** 然后选择 **"操作** -> **连接**"以连接到虚拟机。  默认用户名是**msfadmin，** 密码是**msfadmin**。 


该模板现已更新，并具有道德黑客渗透测试类所需的图像、具有执行渗透测试的工具的图像以及要发现的另一个具有安全漏洞的图像。 模板映像现在可以发布到类。 选择模板页上的 **"发布"** 按钮，将模板发布到实验室。
  

## <a name="cost"></a>成本  
如果要估计本实验的成本，可以使用以下示例： 
 
对于有 25 名学生的班级，有 20 小时的上课时间和 10 小时的家庭作业或作业配额，实验室的价格是： 

25 名学生 = （20 + 10） 小时 = 55 个实验室单位 = 每小时 0.01 USD = 412.50 USD。 

有关定价的详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文介绍了创建道德黑客类实验室的步骤。 它包括设置嵌套虚拟化的步骤，用于在主机虚拟机内创建两个虚拟机以进行穿透式测试。

## <a name="next-steps"></a>后续步骤
设置任何实验室通常采取以下步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)。 

