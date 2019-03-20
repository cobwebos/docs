---
title: 在 Azure 实验室服务的教室实验室中配置使用设置 | Microsoft Docs
description: 了解如何配置实验室的用户数，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: eadbaacd4bb860cd09997775b8d77cbcf0ff17f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181616"
---
# <a name="configure-usage-settings-and-policies"></a>配置使用设置和策略
本文介绍了如何向实验室添加用户，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。 


## <a name="add-users-to-the-lab"></a>将用户添加到实验室
若启用了“限制访问”，请将用户（电子邮件地址）添加到列表。

1. 选择左侧菜单上的“用户”。
2. 在工具栏上选择“添加用户”。 

    ![“添加用户”按钮](../media/how-to-configure-student-usage/add-users-button.png)
1. 在“添加用户”页上，在多个不同的行中输入电子邮件地址，或者在一行中输入以分号分隔的电子邮件地址。 

    ![添加用户电子邮件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 选择“保存”。 可以在列表中看到用户的电子邮件地址及其状态（已注册或未注册）。 

    ![用户列表](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>学生使用的共享注册链接
若要将注册链接发送到学生，使用以下方法之一。 第一种方法演示如何将电子邮件发送到学生注册链接和可选的消息。 第二种方法演示如何获取您可与他人共享所需的任何方式的注册链接。 

如果为实验室启用了“限制访问”，则只有用户列表中的用户可以使用注册链接注册到实验室。 默认情况下启用此选项。 

### <a name="send-email-to-users"></a>向用户发送电子邮件
1. 切换到“用户”视图（如果尚未转到该页）。 
2. 在列表中选择特定或所有用户。 若要选择特定的用户，请在列表的第一列中选中复选框。 若要选择的所有用户，请都选择前面的第一列标题的复选框 (**名称**) 或在列表中都选择的所有用户的所有复选框。
3. 选择**发送邀请**工具栏上。 此外可以将鼠标悬停在列表中一个学生姓名，然后发送电子邮件图标。 

    ![通过电子邮件发送注册链接](../media/tutorial-setup-classroom-lab/send-email.png)
4. 上**通过电子邮件发送注册链接**页上，执行以下步骤： 
    1. 类型**可选消息**想要发送给学生。 电子邮件会自动包括的注册链接。 
    2. 上**通过电子邮件发送注册链接**页上，选择**发送**。 

## <a name="get-registration-link"></a>获取注册链接
1. 在左侧菜单中选择“用户”，切换到“用户”视图。 
2. 选择“获取注册链接”磁贴。

    ![学生注册链接](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在“用户注册”对话框中，选择“关闭”。 
4. 共享**注册链接**与一名学生，以便学生可以注册的类。 

## <a name="view-users-registered-with-the-lab"></a>查看注册到实验室的用户

在左侧的菜单上选择“用户”可查看已在实验室中注册的用户的列表。 

![已在实验室中注册的用户的列表](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>设置每个用户的配额
可以使用以下步骤来设置每个用户的配额： 

1. 选择左侧菜单上的“用户”。
2. 在工具栏上选择“每用户配额: 无限制”。 
3. 在“每用户配额”页面上，选择以下选项之一： 
    1. **无**。 只有在计划的时间内或者当实验室所有者为用户启用了虚拟机时，用户才能使用其虚拟机。
    2. **无限制(默认值)**。 用户可以不受时间限制地使用其虚拟机。
    3. **指定每个用户的小时数**。 除了计划的时间之外，用户还可以将其虚拟机使用设定的小时数（在下面指定）。 如果选择了此选项，请在文本框中输入**小时数**。 

        ![每个用户的小时数](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. 选择“保存”。 
5. 现在可以在工具栏上看到已更改的值：**每个用户的配额: &lt;小时数&gt;**。 

    ![每个用户的配额](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [VM 的计划运行时间](how-to-create-schedules.md)不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划时间之外的时间。 

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户
还可通过上传包含用户电子邮件地址的 CSV 文件来添加用户。

1. 创建一个 CSV 文件，在其中将用户的电子邮件地址放在一个列中。

    ![每个用户的配额](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在实验室的“用户”页面上，在工具栏上选择“上传 CSV”。

    ![“上传 CSV”按钮](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 选择包含用户电子邮件地址的 CSV 文件。 在选择 CSV 文件后选择“打开”时，可以看到以下“添加用户”窗口。 电子邮件地址列表中将填充来自 CSV 文件的电子邮件地址。 

    ![填充有来自 CSV 文件的电子邮件地址的“添加用户”窗口](../media/how-to-configure-student-usage/add-users-window.png)
4. 在“添加用户”窗口中选择“保存”。 
5. 确认你可以在用户列表中看到用户。 

    ![添加的用户的列表](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>管理用户 VM
在学生使用你提供的注册链接向 Azure 实验室服务进行注册后，你可以在“虚拟机”选项卡上查看已分配给学生的虚拟机。 

![已分配给学生的虚拟机](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

可以对学生 VM 执行以下任务： 

- 停止 VM（如果 VM 正在运行）。 
- 启动 VM（如果 VM 已停止）。 
- 连接到 VM。 
- 删除 VM。 
- 查看用户已使用虚拟机的小时数。 

## <a name="update-number-of-virtual-machines-in-lab"></a>更新实验室中的虚拟机数量
若要更新实验室中的虚拟机数量，请在“虚拟机”页面中执行以下步骤：

1. 在左侧菜单中，选择“虚拟机”。 
2. 在工具栏上选择“实验室容量: &lt;number&gt; 台计算机”。 
3. 输入虚拟机的**数量**。
4. 选择“保存”。

    ![实验室中的虚拟机](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
