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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324339"
---
# <a name="add-and-manage-lab-users"></a>添加和管理实验室用户
本文介绍了如何向实验室添加用户，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。 


## <a name="add-users-to-the-lab"></a>将用户添加到实验室

1. 选择左侧菜单上的“用户”。 默认情况下，“限制访问”选项处于启用状态。 当此设置处于启用状态时，用户即使有注册链接也不能注册到实验室，除非该用户在用户列表中。 只有列表中的用户可以使用你发送的注册链接注册到实验室。 在此过程中，请将用户添加到列表。 也可关闭“限制访问”，这样一来，用户只要有注册链接就可以注册到实验室。 
2. 在工具栏上选择 "**添加用户**"，然后选择 "**通过电子邮件地址添加**"。 

    ![“添加用户”按钮](../media/how-to-configure-student-usage/add-users-button.png)
1. 在“添加用户”页上，在多个不同的行中输入电子邮件地址，或者在一行中输入以分号分隔的电子邮件地址。 

    ![添加用户电子邮件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 选择“保存”。 可以在列表中看到用户的电子邮件地址及其状态（已注册或未注册）。 

    ![用户列表](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户
还可通过上传包含用户电子邮件地址的 CSV 文件来添加用户。

1. 创建一个 CSV 文件，在其中将用户的电子邮件地址放在一个列中。

    ![带有用户的 CSV 文件](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在实验室的 "**用户**" 页上，选择工具栏上的 "**添加用户**"，然后选择 "**上载 CSV**"。

    ![“上传 CSV”按钮](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 选择包含用户电子邮件地址的 CSV 文件。 在选择 CSV 文件后选择“打开”时，可以看到以下“添加用户”窗口。 电子邮件地址列表中将填充来自 CSV 文件的电子邮件地址。 

    ![填充有来自 CSV 文件的电子邮件地址的“添加用户”窗口](../media/how-to-configure-student-usage/add-users-window.png)
4. 在“添加用户”窗口中选择“保存”。 
5. 确认你可以在用户列表中看到用户。 

    ![添加的用户的列表](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>向用户发送邀请
若要向学生发送注册链接，请使用以下方法之一。 第一种方法显示了如何使用注册链接和可选消息向学生发送电子邮件。 第二种方法向您演示如何获取注册链接，你可以根据需要以任何方式与他人共享。 

如果为实验室启用了“限制访问”，则只有用户列表中的用户可以使用注册链接注册到实验室。 默认情况下启用此选项。 

### <a name="invite-all-users"></a>邀请所有用户

1. 切换到实验室的 "**用户**" 页。 
2. 从工具栏中选择 "**全部邀请**"。 
3. 输入用户的**消息**。 这是可选步骤。
4. 然后选择 "**发送**"。

    ![邀请所有用户](../media/how-to-configure-student-usage/invite-all.png)

    你将在**用户**列表的 "**邀请**" 列中看到此操作的状态。 邀请电子邮件将包含用户可用于向实验室注册的注册链接。 

### <a name="invite-selected-users"></a>邀请所选用户

1. 在列表中选择一个或多个用户。 
2. 然后，选择工具栏上的 "选择的行" 中显示的**信封**图标（或）选择 "**邀请**"。 

    ![邀请所选用户](../media/how-to-configure-student-usage/invite-selected-users.png)
3. 在 "**通过电子邮件发送邀请**" 窗口中，输入可选**消息**，然后选择 "**发送**"。 

    ![向选定用户发送电子邮件](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    你将在**用户**列表的 "**邀请**" 列中看到此操作的状态。 邀请电子邮件将包含用户可用于向实验室注册的注册链接。

1. 切换到“用户”视图（如果尚未转到该页）。 

## <a name="get-registration-link"></a>获取注册链接
你还可以从门户获取注册链接，并使用你自己的电子邮件客户端应用程序发送该链接。 

1. 在左侧菜单中选择“用户”，切换到“用户”视图。 
2. 选择 **.。。（省略号）** 在工具栏上，选择 "**注册链接**"。

    ![学生注册链接](../media/how-to-configure-student-usage/registration-link-button.png)
1. 在 "**用户注册**" 对话框中，选择 "**复制**" 按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/how-to-configure-student-usage/registration-link.png)
2. 在 "**用户注册**" 对话框中，选择 "**完成**"。 
4. 向学生发送**注册链接**，以便学生可以注册该类。 

## <a name="view-users-registered-with-the-lab"></a>查看注册到实验室的用户

在左侧的菜单上选择“用户”可查看已在实验室中注册的用户的列表。 

![已在实验室中注册的用户的列表](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>为用户设置配额
可以使用以下步骤来设置每个用户的配额： 

1. 如果页面尚未处于活动状态，请在左侧菜单中选择“用户”。 
2. 在工具栏上，选择 "**每个用户的配额： &lt;number @ no__t-2 小时**"。 
3. 在 "**每个用户的配额**" 页上，指定你想要在计划的类时间之外为每个用户（学生）指定的小时数，然后选择 "**保存**"。

    ![每个用户的配额](../media/how-to-configure-student-usage/quota-per-user.png)    
5. 现在，工具栏上已更改的值： "**每个用户的配额： @no__t-小时的 1number" @ no__t-2**。 

    ![每个用户的配额-之后](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 的计划运行时间](how-to-create-schedules.md)不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="set-additional-quota-for-a-specific-user"></a>为特定用户设置附加配额
可以为用户设置单独的配额。 为此，请执行下列步骤：

1. 从 "**用户**" 页上的用户列表中选择一个用户（student）。
2. 然后，从工具栏中选择 "**调整配额**"。 

    ![调整配额按钮](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 输入所选用户或用户的**额外小时**数，然后选择 "**应用**"。 

    ![用户的额外配额](../media/how-to-configure-student-usage/additional-quota.png)
4. 你将在 "**使用情况**" 列中看到用户的更新使用情况。 

    ![用户的新使用情况](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
