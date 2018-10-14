---
title: 使用 Azure 实验室服务设置课堂实验室 | Microsoft Docs
description: 在此教程中，将设置要在课堂中使用的实验室。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/05/2018
ms.author: spelluru
ms.openlocfilehash: 6696d6e7e53e98dfab2a65c7c66825936020f33c
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856626"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教程：设置课堂实验室 
在本教程中，请设置带虚拟机的课堂实验室，供学生在课堂上使用。  

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建课堂实验室
> * 配置课堂实验室
> * 将注册链接发送给学生

## <a name="prerequisites"></a>先决条件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 实验室所有者可以根据[将用户添加为“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)一文中的步骤将其他用户添加为“实验室创建者”角色。


## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的**名称**。 
    2. 指定允许加入实验室的最大**用户数**。 
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
    3. 选择**创建**。 

        ![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在“配置模板”页上，查看实验室创建过程的状态。 在实验室中创建模板最长需要 20 分钟时间。 

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
    1. 若要立即发布模板，请选中“我了解发布后无法修改模板。此过程只能执行一次，最长可能需要一小时”对应的复选框，然后选择“发布”。  

        > [!WARNING]
        > 发布后无法取消发布。 
    2. 若要在以后发布，请选择“保存供以后发布”。 可以在完成向导后发布模板 VM。 有关如何在完成向导后进行配置和发布的详细信息，请参阅[如何管理课堂实验室](how-to-manage-classroom-labs.md)一文中的[发布模板](how-to-manage-classroom-labs.md#publish-the-template)部分。

        ![发布模板](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 可以查看**发布模板的进度**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 成功发布模板后，会看到以下页。 选择“完成”。

    ![发布模板 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 实验室的**仪表板**随即显示。 
    
    ![课堂实验室仪表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 切换到“虚拟机”页，确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>将注册链接发送给学生

1. 在左侧菜单中选择“仪表板”切换到“仪表板”视图。 
2. 选择“用户注册”磁贴。

    ![学生注册链接](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在“用户注册”对话框中，选择“关闭”。 
3. 与某个学生共享注册链接，使其能够注册课程。 


## <a name="next-steps"></a>后续步骤
在本教程中，创建并配置了课堂实验室。 若要了解学生如何使用注册链接访问实验室中的 VM，请转到下一教程：

> [!div class="nextstepaction"]
> [连接到课堂实验室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

