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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 39683c89db57dbeefd190a51415c783d012785e0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303769"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教程：设置课堂实验室 
在本教程中，请设置带虚拟机的课堂实验室，供学生在课堂上使用。  

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建课堂实验室
> * 配置课堂实验室
> * 将注册链接发送给学生

## <a name="prerequisites"></a>先决条件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”角色的成员。 实验室所有者可以根据[将用户添加为“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)一文中的步骤将用户添加为“实验室创建者”角色。


## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。
2. 选择“登录”并输入凭据。 
3. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定课堂实验室的“名称”。 
    2. 选择计划在课堂中使用的虚拟机的“大小”。
    3. 选择用于创建虚拟机的“映像”。
    4. 指定用于登录到实验室中的虚拟机的“默认凭据”。 
    7. 选择“保存”。

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. 随即显示实验室的**仪表板**。 
    
    ![课堂实验室仪表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>配置使用策略

1. 选择“使用策略”。 
2. 在“使用策略”设置中，输入允许使用实验室的“用户数量”。
3. 选择“保存”。 

    ![使用策略](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>设置模板 
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 公开发布此模板，让模板 VM 的实例可供实验室用户使用。 

### <a name="set-title-and-description"></a>设置标题和说明
1. 在“模板”部分，选择与模板相对应的“编辑”（铅笔图标）。 
2. 在“用户视图”窗口中，输入模板的“标题”。
3. 输入模板的“说明”。
4. 选择“保存”。

    ![课堂实验室说明](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>设置模板 VM
 请先连接到模板 VM 并在其上安装任何必需的软件，然后再将其提供给学生。 

1. 等待模板虚拟机就绪。 该虚拟机就绪以后，“启动”按钮会变为已启用状态。 若要启动此 VM，请选择“启动”。

    ![启动模板 VM](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 若要连接到 VM，请选择“连接”，然后按说明操作。 

    ![连接到模板 VM](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 安装学生进行实验室操作所需的任何软件（例如，Visual Studio、Azure 存储资源管理器等）。 
2. 断开与模板 VM 的连接（关闭远程桌面会话）。 
3. 选择“停止”，**停止**此模板。 

    ![停止模板 VM](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>发布模板 
发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 

1. 在“模板”部分选择“发布”。 

    ![发布模板 VM](../media/tutorial-setup-classroom-lab/public-access.png)
1. 在“发布”窗口选择“已发布”选项。 
2. 现在选择“发布”按钮。 此过程可能需要一段时间，具体取决于要创建的 VM 数，该数目就是允许进入实验室的用户数。
    
    > [!IMPORTANT]
    > 将模板设置为公用以后，便不能将其访问权限更改为专用。 
4. 切换到“虚拟机”页，确认是否看到五个虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 

    ![虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. 等待 VM 创建完毕。 它们应该处于“已停止”状态。 可以在此页上启动一个学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>将注册链接发送给学生

1. 切换到“仪表板”视图。 
2. 选择“用户注册”磁贴。

    ![学生注册链接](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在“用户注册”对话框中，选择“关闭”。 


## <a name="next-steps"></a>后续步骤
在本教程中，创建并配置了课堂实验室。 若要了解学生如何使用注册链接访问实验室中的 VM，请转到下一教程：

> [!div class="nextstepaction"]
> [连接到课堂实验室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

