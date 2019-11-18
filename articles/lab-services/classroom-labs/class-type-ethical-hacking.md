---
title: 使用 Azure 实验室服务设置道德黑客实验室 |Microsoft Docs
description: 了解如何使用 Azure 实验室服务设置实验室来讲授道德黑客。
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
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133176"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>设置实验室来讲授道德攻击类 
本文介绍如何设置一个侧重于道德攻击辩论方的类。 渗透测试是道德黑客社区使用的一种做法，当某人试图获得对系统或网络的访问权限以证明恶意攻击者可能利用的漏洞时，就会进行渗透测试。 

在道德黑客课程中，学生可以学习抵御漏洞的新式技术。 每个学生获取一个 Windows Server 主机虚拟机，该虚拟机具有两个嵌套虚拟机–一台虚拟机具有[Metasploitable3](https://github.com/rapid7/metasploitable3)映像，另一台计算机具有[Kali Linux](https://www.kali.org/)映像。 Metasploitable 虚拟机用于开发目的，Kali 虚拟机提供对执行取证任务所需的工具的访问。

本文包含两个主要部分。 第一部分介绍如何创建教室实验室。 第二部分介绍如何创建启用了嵌套虚拟化的模板计算机以及所需的工具和映像。 在这种情况下，计算机上的 Metasploitable 映像和 Kali Linux 映像已启用 Hyper-v 以承载映像。

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，需要一个 Azure 订阅才能开始使用。 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户或使用现有帐户。 请参阅以下教程，了解如何创建新的实验室帐户：[设置实验室帐户教程](tutorial-setup-lab-account.md)。

按照[本教程](tutorial-setup-classroom-lab.md)创建新的实验室，然后应用以下设置：

| 虚拟机大小 | 映像 |
| -------------------- | ----- | 
| 中（嵌套虚拟化） | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>模板计算机 

创建模板计算机后，启动计算机并连接到该计算机以完成以下三个主要任务。 
 
1. 设置计算机以进行嵌套虚拟化。 它将启用所有适当的 windows 功能（如 Hyper-v），并设置网络，以便 Hyper-v 映像能够彼此通信和 internet。
2. 设置[Kali](https://www.kali.org/) Linux 映像。 Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。
3. 设置 Metasploitable 映像。 在此示例中，将使用[Metasploitable3](https://github.com/rapid7/metasploitable3)图像。 将创建此映像以故意产生安全漏洞。

[实验室服务道德黑客脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)中提供了可自动执行上述任务的脚本。

### <a name="prepare-template-machine-for-nested-virtualization"></a>准备用于嵌套虚拟化的模板计算机
按照[本文](how-to-enable-nested-virtualization-template-vm.md)中的说明准备模板虚拟机以进行嵌套虚拟化。 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用 Kali Linux 映像设置嵌套虚拟机
Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。

1. 从[https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下载映像。  
    1. 下载适用于 Hyper-v 的**Kali Linux hyper-v 64 位**。
    1. 提取7z 文件。  如果还没有 7 zip，请从[https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)下载。 记住提取文件夹的位置，因为稍后需要用到它。
2. 从 "管理工具" 打开**Hyper-v 管理器**。
1. 选择 "**操作**"，然后选择 "**导入虚拟机**"。 
1. 在 "**导入虚拟机**" 向导的 "**查找文件夹**" 页上，选择包含 Kali Linux 映像的已提取文件夹的位置。

    ![定位文件夹对话框](../media/class-type-ethical-hacking/locate-folder.png)
1. 在 "**选择虚拟机**" 页上，选择 Kali Linux 映像。  在这种情况下，映像是**kali-2019.3-hyperv**。

    ![选择 Kali 映像](../media/class-type-ethical-hacking/select-kali-image.png)
1.  在 "**选择导入类型**" 页上，选择 **"复制虚拟机（创建新的唯一 ID）** "。

    ![选择导入类型](../media/class-type-ethical-hacking/choose-import-type.png)
1. 接受 "**选择虚拟机文件的文件夹**" 的默认设置，并**选择 "存储虚拟硬盘的文件夹**" 页面，然后选择 "**下一步**"。
1. 在 "**连接网络**" 页上，选择之前在本文的 "**用于嵌套虚拟化的准备模板**" 部分中创建的**LabServicesSwitch** ，然后选择 "**下一步**"。

    ![连接网络页](../media/class-type-ethical-hacking/connect-network.png)
1. 在 "**摘要**" 页上选择 "**完成**"。 等待复制和导入操作完成。 现在，Hyper-v 中会提供 Kali Linux 虚拟机。
1. 从**Hyper-v 管理器**中，选择 "**操作**" -> **启动**"，然后选择"**操作**" -> "**连接**"以连接到虚拟机。  
12. 默认用户名是 `root`，密码是 `toor`。 

    > [!NOTE]
    > 如果需要解锁图像，请按 CTRL 键并向上拖动鼠标。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>使用 Metasploitable 映像设置嵌套 VM  
Rapid7 Metasploitable 映像是特意配置了安全漏洞的映像。 将使用此映像来测试和查找问题。 以下说明介绍了如何使用预先创建的 Metasploitable 映像。 但是，如果需要更新版本的 Metasploitable 映像，请参阅[https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)。

1. 导航到 [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填写表单以下载图像，然后选择 "**提交**" 按钮。
1. 选择 "**立即下载 Metasploitable** " 按钮。
1. 下载 zip 文件时，提取 zip 文件，并记住该位置。
1. 将提取的 vmdk 文件转换为 vhdx 文件，以便可以使用 Hyper-v。 为此，请打开具有管理权限的 PowerShell，导航到 vmdk 文件所在的文件夹，然后按照以下说明进行操作：
    1. 下载[Microsoft 虚拟机转换器](https://www.microsoft.com/download/details.aspx?id=42497)，并在出现提示时运行 mvmc_setup .msi 文件。
    1. 导入 PowerShell 模块。  安装模块的默认位置为 C:\Program Files\Microsoft Virtual Machine 转换器 \

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. 将 vmdk 转换为可由 Hyper-v 使用的 vhd 文件。 此操作可能需要几分钟。
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. 将新创建的 metasploitable 复制到 C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\。 
1. 创建新的 Hyper-v 虚拟机。
    1. 打开**Hyper-v 管理器**。
    1. 选择 "**操作**" -> "**新建** -> "**虚拟机**。
    1. 在**新建虚拟机向导**的 "**开始之前**" 页上，单击 "**下一步**"。
    1. 在 "**指定名称和位置**" 页上，输入**Metasploitable**作为**名称**，然后选择 "**下一步**"。

        ![新建 VM 映像向导](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 "**指定生成**" 页上，接受默认值，然后选择 "**下一步**"。
    1. 在 "**分配内存**" 页上，为**启动内存**输入**512 MB** ，然后选择 "**下一步**"。 

        ![“分配内存”页](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 "**配置网络**" 页上，将 "连接" 保留为 "**未连接**"。 稍后将设置网络适配器。
    1. 在 "**连接虚拟硬盘**" 页上，选择 "**使用现有虚拟硬盘**"。 浏览到在上一步中创建的**metasploitable**文件的位置，然后选择 "**下一**步"。 

        ![连接虚拟网络磁盘页](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 "**正在完成新建虚拟机向导**" 页上，选择 "**完成**"。
    1. 创建虚拟机后，请在 Hyper-v 管理器中选择它。 不要打开计算机。  
    1. 选择**操作** -> **设置**。
    1. 在的 " **Metasploitable 设置**" 对话框中，选择 "**添加硬件**"。 
    1. 选择 "**旧版网络适配器**"，然后选择 "**添加**"。

        ![网络适配器页](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 "**旧版网络适配器**" 页上，选择 " **LabServicesSwitch** " 作为 "**虚拟交换机**" 设置，然后选择 **"确定"** 。 为 "**嵌套虚拟化的准备模板**" 部分中的 hyper-v 准备模板计算机时创建了 LabServicesSwitch。

        ![旧版网络适配器页](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable 映像现在已可供使用。 从**Hyper-v 管理器**中，选择 "**操作**" -> **启动**"，然后选择"**操作**" -> "**连接**"以连接到虚拟机。  默认用户名为**msfadmin** ，密码为**msfadmin**。 


该模板现已更新，并具有符合道德的黑客渗透测试类所需的图像、包含用于执行渗透测试的工具的映像，以及另一个包含安全漏洞的映像。 现在可以将模板映像发布到类。 选择 "模板" 页上的 "**发布**" 按钮，将模板发布到实验室。
  

## <a name="cost"></a>成本  
如果要估计此实验室的成本，可以使用以下示例： 
 
对于具有20个小时的计划类时间和10小时配额（适用于家庭或分配）的25名学生，实验室的价格为： 

25名学生 * （20 + 10）小时 * 55 实验室单位 * 0.01 美元/小时 = 412.50 美元。 

有关定价的详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文指导你完成为道德攻击类创建实验室的步骤。 它包括设置嵌套虚拟化的步骤，用于在主机虚拟机内创建两个虚拟机以进行渗透测试。

## <a name="next-steps"></a>后续步骤
下一步是设置任何实验室的常见步骤：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)。 

