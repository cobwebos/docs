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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142662"
---
# <a name="configure-usage-settings-and-policies"></a>配置使用设置和策略
本文介绍了如何配置实验室的用户数，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>指定允许加入实验室的用户数

1. 选择“使用策略”。 
2. 在“使用策略”设置中，输入允许使用实验室的“用户数量”。
3. 选择“保存”。 

    ![使用策略](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>将注册链接发送给学生

1. 切换到“仪表板”视图。 
2. 选择“用户注册”磁贴。

    ![学生注册链接](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 学生使用该链接导航到可以帮助他们在实验室中进行注册的页面。 

    ![学生注册链接](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在“用户注册”对话框中，选择“关闭”。 

## <a name="view-users-registered-with-the-lab"></a>查看注册到实验室的用户

在左侧的菜单上选择“用户”可查看已在实验室中注册的用户的列表。 

![已在实验室中注册的用户的列表](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>设置每个用户的配额

1. 在左侧的菜单上选择“用户设置”。
2. 选择“每个用户的配额”磁贴。 
3. 选择“限制用户可以使用 VM 的小时数”。 
4. 对于“希望为每个用户提供多少小时?”，输入小时数，然后选择“保存”。 

    ![每个用户的小时数](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. 现在可以在“每个用户的配额”磁贴上看到小时数。 

    ![每个用户的配额](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>管理用户 VM
在学生使用你提供的注册链接向 Azure 实验室服务进行注册后，你可以在“虚拟机”选项卡上查看已分配给学生的虚拟机。 

![已分配给学生的虚拟机](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

可以对学生 VM 执行以下任务： 

- 停止 VM（如果 VM 正在运行）。 
- 启动 VM（如果 VM 已停止）。 
- 连接到 VM。 
- 删除 VM。 
- 查看用户已使用虚拟机的小时数。 


## <a name="next-steps"></a>后续步骤
开始使用 Azure 实验室服务设置实验室：

- [设置课堂实验室](how-to-manage-classroom-labs.md)
- [设置实验室](../tutorial-create-custom-lab.md)
