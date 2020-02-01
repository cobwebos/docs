---
title: 使用 Azure 实验室服务设置课堂实验室 | Microsoft Docs
description: 在本教程中，我们使用 Azure 实验室服务设置带虚拟机的课堂实验室，供学生在课堂上使用。
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719171"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教程：设置教室实验室 
在本教程中，请设置带虚拟机的课堂实验室，供学生在课堂上使用。  

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建课堂实验室
> * 将用户添加到实验室
> * 为实验室设置计划
> * 向学生发送邀请电子邮件

## <a name="prerequisites"></a>必备条件
只有充当下述角色之一的成员才能在实验室帐户中设置课堂实验室：所有者、实验室创建者或参与者。 用来创建实验室帐户的帐户会自动添加到所有者角色。

实验室所有者可以将其他用户添加到“实验室创建者”角色。  例如，实验室所有者可以将教授添加到“实验室创建者”角色。 然后，教授可以为其课堂创建带 VM 的实验室。 学生使用从教授处接收的注册链接注册到实验室。 注册以后，他们可以使用实验室中的 VM 完成课堂作业和家庭作业。 有关如何将用户添加为“实验室创建者”角色的详细步骤，请参阅[将用户添加为“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 请注意，尚不支持 Internet Explorer 11。 
2. 选择“登录”  并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 选择“新建实验室”。  
    
    ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在“新建实验室”窗口中，执行以下操作：  ： 
    1. 指定实验室的**名称**，然后选择“下一步”。   

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        如果选择 Linux 映像，可以看到一个用来为该映像启用远程桌面连接的选项。 有关详细信息，请参阅[启用适用于 Linux 的远程桌面连接](how-to-enable-remote-desktop-linux.md)。
    2. 在“虚拟机凭据”页上，指定实验室中所有 VM 的默认凭据。  指定用户的**名称**和**密码**，然后选择“下一步”。   

        ![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 在“实验室策略”页上，  输入为每个用户分配的在实验室计划时间之外的小时数（**每个用户的配额**），然后选择“完成”。  

        ![每个用户的配额](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 此时会看到以下屏幕，显示模板 VM 的创建状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![模板 VM 的创建状态](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在“模板”页上执行以下步骤：  对于此教程来说，这些步骤是**可选**的。

    2. 选择“连接”以连接到模板 VM。  如果它是 Linux 模板 VM，请选择是要使用 SSH 还是 RDP（如果已启用 RDP）来建立连接。
    1. 选择“重置密码”  以重置 VM 的密码。 
    1. 在模板 VM 上安装并配置软件。 
    1. **停止** VM。  
    1. 输入模板的**说明**
10. 在“模板”页上的工具栏中选择“发布”  。  

    ![“发布模板”按钮](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
8. 在“发布模板”  页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。  

    ![发布模板 - VM 数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 可以在页面上查看模板的**发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 选择左侧菜单上的“虚拟机”或选择“虚拟机”磁贴，切换到“虚拟机池”页。   确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。  可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    在此页面上执行以下任务（请勿执行本教程的这些步骤。 这些步骤仅供参考。）： 
    
    1. 若要更改实验室容量（实验室中的 VM 数），请在工具栏上选择“实验室容量”。 
    2. 若要一次启动所有 VM，请在工具栏上选择“全部启动”。  
    3. 若要启动特定 VM，请在“状态”  中选择下拉箭头  ，然后选择“启动”。 也可先在第一列中选择一个 VM，然后在工具栏上选择“启动”，以这种方式启动 VM。 

    有关创建和管理模板，以及设置和管理学生虚拟机的详细信息，请参阅以下文章： 
    
    - [创建和管理课堂实验室模板](how-to-create-manage-template.md)
    - [设置和管理虚拟机池](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>将用户添加到实验室

1. 选择左侧菜单上的“用户”  。 默认情况下，“限制访问”选项处于启用状态。  当此设置处于启用状态时，用户即使有注册链接也不能注册到实验室，除非该用户在用户列表中。 只有列表中的用户可以使用你发送的注册链接注册到实验室。 在此过程中，请将用户添加到列表。 也可关闭“限制访问”，这样一来，用户只要有注册链接就可以注册到实验室。  
2. 选择工具栏中的“添加用户”，然后选择“通过电子邮件地址添加”。   

    ![“添加用户”按钮](../media/how-to-configure-student-usage/add-users-button.png)
1. 在“添加用户”页上，  在多个不同的行中输入电子邮件地址，或者在一行中输入以分号分隔的电子邮件地址。 

    ![添加用户电子邮件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 选择“保存”。  可以在列表中看到用户的电子邮件地址及其状态（已注册或未注册）。 

    ![用户列表](../media/how-to-configure-student-usage/users-list-new.png)

    将用户注册到实验室后，列表中会显示其姓名。 
    
## <a name="set-a-schedule-for-the-lab"></a>为实验室设置计划
为实验室创建计划事件，这样实验室中的 VM 就会在特定时间自动启动/停止。 此前指定的用户配额是在此计划时间之外为每个用户分配的额外时间。 

1. 切换到“日程安排”  页，再选择工具栏上的“添加计划事件”  。 

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/add-schedule-button.png)
2. 在“添加计划事件”页上执行以下步骤： 
    1. 确认已选择“标准”作为“事件类型”。    
    2. 指定课堂**开始日期**。 
    4. 指定“启动时间”  ，这是你希望 VM 启动的时间。
    5. 指定“停止时间”  ，这是 VM 将要关闭的时间。 
    6. 指定“时区”  ，该时区适用于已指定的启动时间和停止时间。 
3. 在同一“添加计划事件”页上的“重复”部分，选择当前计划。    

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/select-current-schedule.png)
5. 在“重复”  对话框中，执行以下步骤：
    1. 对于“重复”字段，请确认已设置“每周”   。 
    2. 选择希望计划生效的日期。 在下面的示例中，选中的是“星期一”至“星期五”。 
    3. 选择计划的**结束日期**。
    8. 选择“保存”。  

        ![设置重复计划](../media/how-to-create-schedules/set-repeat-schedule.png)

3. 现在，请在“添加计划事件”页上  输入该计划的任何说明或备注作为“备注(可选)”。  
4. 在“添加计划事件”  页上，选择“保存”  。 

    ![每周日程安排](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. 在日历中导航到开始日期，确认是否已设置该计划。
    
    ![日历中的计划](../media/how-to-create-schedules/schedule-calendar.png)

    有关创建和管理课堂计划的详细信息，请参阅[创建和管理课堂实验室的计划](how-to-create-schedules.md)。

## <a name="send-invitation-emails-to-students"></a>向学生发送邀请电子邮件

1. 切换到“用户”视图（如果尚未转到该页），然后在工具栏上选择“全部邀请”。   

    ![选择学生](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在“通过电子邮件发送邀请”页上，输入可选的消息，然后选择“发送”。   电子邮件自动包含注册链接。 若要获取此注册链接，可以选择工具栏上的“... (省略号)”，然后选择“注册链接”。   

    ![通过电子邮件发送注册链接](../media/tutorial-setup-classroom-lab/send-email.png)
4. 可以在“用户”列表中查看**邀请**状态。  状态应更改为“正在发送”，然后更改为“已在 &lt;日期&gt; 发送”。   

    若要详细了解如何将学生添加到课堂并管理其对实验室的使用，请参阅[如何配置学生对实验室的使用](how-to-configure-student-usage.md)。

## <a name="next-steps"></a>后续步骤
在本教程中，创建并配置了课堂实验室。 若要了解学生如何使用注册链接访问实验室中的 VM，请转到下一教程：

> [!div class="nextstepaction"]
> [连接到课堂实验室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

