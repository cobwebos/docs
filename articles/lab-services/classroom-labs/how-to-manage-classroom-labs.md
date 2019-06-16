---
title: 管理 Azure 实验室服务中的教室实验室 | Microsoft Docs
description: 了解如何创建和配置教室实验室、如何查看所有教室实验室、如何与实验室用户共享注册链接，或者如何删除实验室。
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 6ba41132c93ebdb2578bafb100416ca3fe579298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123295"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 实验室服务中的教室实验室 
本文介绍如何创建和删除教室实验室。 它还说明如何查看实验室帐户中的所有教室实验室。 

## <a name="prerequisites"></a>必备组件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”  角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 实验室所有者可以按照以下文章中的步骤将其他用户添加到“实验室创建者”角色：[将用户添加到“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 请注意，尚不支持 Internet Explorer 11。 
2. 选择“登录”  。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码   。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 在“新建实验室”窗口中，执行以下操作：  ： 
    1. 指定实验室的**名称**。 
    2. 在实验室中指定最大**虚拟机数**。 以后可以增加或减少实验室中的虚拟机数。 
    6. 选择“保存”。 

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在“选择虚拟机规范”页上执行以下步骤： 
    1. 选择在实验室中创建的虚拟机 (VM) 的**大小**。 目前，**小型**，**中等**，**中等 （虚拟化）** ，**大型**，并**GPU**大小允许使用。 有关详细信息，请参阅[VM 大小](#vm-sizes)部分。
    1. 选择要在其中创建 VM 的**区域**。 
    1. 选择用于在实验室中创建 VM 的 **VM 映像**。 如果选择 Linux 映像，可以看到一个用来为该映像启用远程桌面连接的选项。 有关详细信息，请参阅[启用适用于 Linux 的远程桌面连接](how-to-enable-remote-desktop-linux.md)。
    1. 选择“**下一步**”。

        ![指定 VM 规范](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. 在“设置凭据”页上，指定实验室中所有 VM 的默认凭据。  
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 禁用**的所有虚拟机中使用相同的密码**选项如果想要设置其自己的密码的学生。 此步骤是**可选的**。 

        教师可以选择在实验室中的所有 vm 使用相同的密码或允许学生将密码设置为其虚拟机。 默认情况下，此设置可用于 Ubuntu 除外的所有 Windows 和 Linux 映像。 当选择**Ubuntu** VM，此设置被禁用，因此将提示您学生在第一次登录时设置密码。
    1. 选择“创建”  。 

        ![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在“配置模板”页上，查看实验室创建过程的状态。  在实验室中创建模板最长需要 20 分钟时间。 实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。  

    ![配置模板](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 配置完模板后，会看到以下页： 

    ![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 在本教程中，以下步骤是可选步骤： 
    2. 选择“连接”以连接到模板 VM。  如果它是 Linux 模板 VM，请选择是要使用 SSH 还是 RDP（如果已启用 RDP）来建立连接。
    1. 选择**重置密码**vm 重置密码。 
    1. 在模板 VM 上安装并配置软件。 
    1. **停止** VM。  
    1. 输入模板的**说明**
9. 在模板页上选择“下一步”。  
10. 在“发布模板”页上执行以下操作。  
    1. 若要立即发布模板，请选中“我了解发布后无法修改模板。  此过程只能执行一次，最长可能需要一小时”对应的复选框，然后选择“发布”。   发布此模板，让模板 VM 的实例可供实验室用户使用。

        > [!WARNING]
        > 发布后无法取消发布。 
    2. 若要在以后发布，请选择“保存供以后发布”。  可以在完成向导后发布模板 VM。 有关如何在完成向导后进行配置和发布的详细信息，请参阅[如何管理课堂实验室](how-to-manage-classroom-labs.md)一文中的“发布模板”部分。

        ![发布模板](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 可以查看**发布模板的进度**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 成功发布模板后，会看到以下页。 选择“完成”  。

    ![发布模板 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 实验室的**仪表板**随即显示。 
    
    ![课堂实验室仪表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 切换到“虚拟机”页，  确认看到这些虚拟机处于“未分配”状态。  这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。  可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>VM 大小  

| 大小 | 核心数 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小 | 2 | 3.5 GB | 此大小最适合用于命令行中，打开 web 浏览器中，低流量 web 服务器、 小型到中型数据库。 |
| 中型 | 4 | 7 GB | 此大小在最适合用于关系数据库、 内存中缓存和分析 | 
| 中等 （嵌套虚拟化） | 4 | 16 GB | 此大小最适合用于关系数据库、 内存中缓存和分析。 此大小还支持嵌套虚拟化。 <p>此大小可以在其中每个学生需要多个 Vm 在方案中使用。 教师可以使用嵌套虚拟化来设置虚拟机内部的几个嵌套的小型虚拟机。 </p> |
| 大型 | 8 | 32 GB | 此大小最适合用于需要更快的 Cpu、 更好的本地磁盘性能、 大型数据库、 较大内存缓存的应用程序。 此大小还支持嵌套虚拟化 |  
| GPU | 12 | 112 GB | 此大小在最适合用于计算密集型、 图形密集型和可视化工作负荷 | 

## <a name="view-all-classroom-labs"></a>查看所有教室实验室
1. 导航到 [Azure 实验室服务门户](https://labs.azure.com)。
2. 选择“登录”  。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码   。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 确认在所选实验室帐户中看到所有实验室。 

    ![所有实验室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用顶部的下拉列表选择另一实验室帐户。 此时会在所选实验室帐户中看到实验室。 

## <a name="delete-a-classroom-lab"></a>删除教室实验室
1. 在实验室对应的磁贴中，选择角落里的三个点 (...)。 

    ![选择实验室](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. 选择“删除”。  

    ![“删除”按钮](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在“删除实验室”对话框中，选择“删除”。   

    ![“删除”对话框](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>切换到另一教室实验室
若要从当前教室实验室切换到另一教室实验室，请在顶部的实验室帐户中选择实验室下拉列表。

![从顶部的下拉列表中选择实验室](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

