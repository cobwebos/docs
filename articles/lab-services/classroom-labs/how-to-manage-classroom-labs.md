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
ms.topic: how-to
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: cc937589b2fc4f394b44cf6890a352d770751d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502036"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 实验室服务中的教室实验室 
本文介绍如何创建和删除教室实验室。 它还说明如何查看实验室帐户中的所有教室实验室。 

## <a name="prerequisites"></a>先决条件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”**** 角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 实验室所有者可以根据[将用户添加为“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)一文中的步骤将其他用户添加为“实验室创建者”角色。

## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 IE1 还不受支持。 
2. 选择“登录”**** 并输入凭据。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码********。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 选择“新建实验室”。**** 
    
    ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. 在“新建实验室”窗口中，执行以下操作：****： 
    1. 指定实验室的**名称**。 
    2. 选择类所需的**虚拟机的大小**。 有关可用大小的列表，请参阅 VM[大小](#vm-sizes)部分。 
    3. 选择要用于课堂实验室的**虚拟机映像**。 如果选择 Linux 映像，可以看到一个用来为该映像启用远程桌面连接的选项。 有关详细信息，请参阅[启用适用于 Linux 的远程桌面连接](how-to-enable-remote-desktop-linux.md)。
    4. 查看页面上显示**的每小时总价**。 
    6. 选择“保存”。****

        ![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > 如果实验室帐户配置为[允许实验室创建者选择实验室位置](allow-lab-creator-pick-lab-location.md)选项，您将看到一个选项，用于选择实验室的位置。 
4. 在“虚拟机凭据”页上，指定实验室中所有 VM 的默认凭据。****
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 如果您希望学生设置自己的密码，请**禁用"对所有虚拟机使用相同的密码**"选项。 此步骤是**可选的**。 

        教师可以选择对实验室中的所有 VM 使用相同的密码，或者允许学生为其 VM 设置密码。 默认情况下，此设置已针对除 Ubuntu 以外的所有 Windows 和 Linux 映像启用。 选择**Ubuntu** VM 时，此设置将被禁用，因此学生首次登录时将提示他们设置密码。  

        ![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 然后，在**虚拟机凭据**页上选择 **"下一步**"。 
5. 在 **"实验室策略"** 页上，执行以下步骤：
    1. 输入为实验室计划时间以外的每个用户分配的小时数（**每个用户的配额**）。 
    2. 对于 **"自动关闭虚拟机"** 选项，请指定是否要在用户断开连接时自动关闭 VM。 您还可以指定 VM 应等待用户重新连接的时间，然后再自动关闭。 有关详细信息，请参阅[在断开连接时启用 VM 的自动关闭](how-to-enable-shutdown-disconnect.md)。
    3. 然后，选择 **"完成**"。 

        ![每个用户的配额](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 此时会看到以下屏幕，显示模板 VM 的创建状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![模板 VM 的创建状态](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 **"模板"** 页上，执行以下步骤：这些步骤对于本教程是**可选**的。

    2. 选择“连接”以连接到模板 VM。**** 如果它是 Linux 模板 VM，请选择是要使用 SSH 还是 RDP（如果已启用 RDP）来建立连接。
    1. 选择“重置密码”**** 以重置 VM 的密码。 
    1. 在模板 VM 上安装并配置软件。 
    1. **停止**VM。  
    1. 输入模板的**说明**
10. 在“模板”页上的工具栏中选择“发布”****。**** 

    ![“发布模板”按钮](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
8. 在“发布模板”**** 页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。**** 

    ![发布模板 - VM 数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 可以在页面上查看模板的**发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 选择左侧菜单上的“虚拟机”或选择“虚拟机”磁贴，切换到“虚拟机池”页。**** **** 确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。**** 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    在此页面上执行以下任务（请勿执行本教程的这些步骤。 这些步骤仅供参考。）： 
    
    1. 若要更改实验室容量（实验室中的 VM 数），请在工具栏上选择“实验室容量”。****
    2. 若要一次启动所有 VM，请在工具栏上选择“全部启动”。**** 
    3. 若要启动特定 VM，请在“状态”**** 中选择下拉箭头****，然后选择“启动”。 也可先在第一列中选择一个 VM，然后在工具栏上选择“启动”，以这种方式启动 VM。****                

### <a name="vm-sizes"></a>VM 大小  

| 大小 | 核心数 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小型 | 2 | 3.5 GB | 此大小最适合命令行、打开 Web 浏览器、低流量 Web 服务器、中小型数据库。 |
| 中型 | 4 | 7 GB | 此大小最适合关系数据库、内存缓存和分析 | 
| 中等（嵌套虚拟化） | 4 | 16 GB | 此大小最适合关系数据库、内存缓存和分析。 此大小还支持嵌套虚拟化。 <p>此大小可用于每个学生需要多个 VM 的情况。 教师可以使用嵌套虚拟化在虚拟机内设置几个小型嵌套虚拟机。 </p> |
| 大型 | 8 | 32 GB | 此大小最适合需要更快 CPU、更好的本地磁盘性能、大型数据库和大型内存缓存的应用程序。 此大小还支持嵌套虚拟化 |  
| 小型 GPU（可视化） | 6 | 56 GB | 此大小最适合使用 OpenGL 和 DirectX 等框架进行远程可视化、流式传输、游戏和编码。 | 
| 小型 GPU（计算） | 6 | 56 GB | 此大小最适合计算密集型和网络密集型应用，如人工智能和深度学习应用。 | 
| 中型 GPU（可视化） | 12 | 112 GB | 此大小最适合使用 OpenGL 和 DirectX 等框架进行远程可视化、流式传输、游戏和编码。 | 

> [!NOTE]
> 使用 GPU 映像创建实验室时，Azure 实验室服务会自动安装和配置必要的 GPU 驱动程序。  

## <a name="view-all-classroom-labs"></a>查看所有教室实验室
1. 导航到 [Azure 实验室服务门户](https://labs.azure.com)。
2. 选择**登录**。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码********。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 确认在所选实验室帐户中看到所有实验室。 在实验室的磁贴上，您可以看到实验室中的虚拟机数和每个用户的配额（超出计划时间）。

    ![所有实验室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用顶部的下拉列表选择另一实验室帐户。 此时会在所选实验室帐户中看到实验室。 

## <a name="delete-a-classroom-lab"></a>删除教室实验室
1. 在实验室的磁贴上，选择角中的三个点 （...），然后选择 **"删除**"。 

    ![“删除”按钮](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在 **"删除实验室"** 对话框中，选择 **"删除**"以继续删除。 

## <a name="switch-to-another-classroom-lab"></a>切换到另一教室实验室
若要从当前教室实验室切换到另一教室实验室，请在顶部的实验室帐户中选择实验室下拉列表。

![从顶部的下拉列表中选择实验室](../media/how-to-manage-classroom-labs/switch-lab.png)

您还可以使用此下拉列表中**的"新建"实验室**创建新实验室。 

> [!NOTE]
> 您还可以使用 Az.LabServices PowerShell 模块（预览版）来管理实验室。 有关详细信息，请参阅[GitHub 上的 Az.LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

要切换到其他实验室帐户，请选择实验室帐户旁边的下拉列表，然后选择其他实验室帐户。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

