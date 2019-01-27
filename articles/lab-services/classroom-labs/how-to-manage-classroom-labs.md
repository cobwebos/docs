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
ms.date: 01/16/2019
ms.author: spelluru
ms.openlocfilehash: 5c1207b1b21e2d2ee229f5bea068b99f3b3218b1
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389121"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 实验室服务中的教室实验室 
本文介绍如何创建和删除教室实验室。 它还说明如何查看实验室帐户中的所有教室实验室。 

## <a name="prerequisites"></a>先决条件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 实验室所有者可以按照以下文章中的步骤将其他用户添加到“实验室创建者”角色：[将用户添加到“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的**名称**。 
    2. 指定允许加入实验室的**用户数**。 
    6. 选择“保存”。

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在“选择虚拟机规范”页上执行以下步骤：
    1. 选择在实验室中创建的虚拟机 (VM) 的**大小**。 
    2. 选择要在其中创建 VM 的**区域**。 
    3. 选择用于在实验室中创建 VM 的 **VM 映像**。 
    4. 选择“**下一步**”。

        ![指定 VM 规范](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. 在“设置凭据”页上，指定实验室中所有 VM 的默认凭据。 
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 选择“创建”。 

        ![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在“配置模板”页上，查看实验室创建过程的状态。 在实验室中创建模板最长需要 20 分钟时间。 实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。  

    ![配置模板](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 配置完模板后，会看到以下页： 

    ![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 在本教程中，以下步骤是可选步骤： 
    1. 选择“启动”来启动模板 VM。
    2. 选择“连接”以连接到模板 VM。 
    3. 在模板 VM 上安装并配置软件。 
    4. **停止** VM。  
    5. 输入模板的**说明**

        ![“配置模板”页上的“下一步”](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 在模板页上选择“下一步”。 
10. 在“发布模板”页上执行以下操作。 
    1. 若要立即发布模板，请选中“我了解发布后无法修改模板。此过程只能执行一次，最长可能需要一小时”对应的复选框，然后选择“发布”。  发布此模板，让模板 VM 的实例可供实验室用户使用。

        > [!WARNING]
        > 发布后无法取消发布。 
    2. 若要在以后发布，请选择“保存供以后发布”。 可以在完成向导后发布模板 VM。 有关如何在完成向导后进行配置和发布的详细信息，请参阅[如何管理课堂实验室](how-to-manage-classroom-labs.md)一文中的[发布模板](#publish-the-template)部分。

        ![发布模板](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 可以查看**发布模板的进度**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 成功发布模板后，会看到以下页。 选择“完成”。

    ![发布模板 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 实验室的**仪表板**随即显示。 
    
    ![课堂实验室仪表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 切换到“虚拟机”页，确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>查看所有教室实验室
1. 导航到 [Azure 实验室服务门户](https://labs.azure.com)。
2. 选择“登录”。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
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
 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

