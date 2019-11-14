---
title: 在 Azure 实验室服务中跟踪实验室使用情况 | Microsoft Docs
description: 在本教程中，你以实验室创建者/所有者的身份跟踪实验室的使用情况。
services: lab-services
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580227"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>教程：在 Azure 实验室服务中跟踪实验室使用情况
本教程介绍实验室创建者/所有者如何跟踪实验室使用情况。

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 查看注册到实验室的用户
> * 查看实验室中 VM 的使用情况
> * 管理学生 VM 


## <a name="view-users-registered-with-the-lab"></a>查看注册到实验室的用户

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”  并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
3. 在“我的实验室”页上，选择要跟踪其使用情况的实验室。  
4. 选择左侧菜单上的“用户”，或选择“用户”磁贴  。  此时会看到注册到实验室的学生。 选择“注册链接”，复制该链接并将其发送给尚未注册到实验室的新学生。  

    ![注册的用户](../media/tutorial-track-usage/registered-users.png)

    有关添加和管理实验室用户的详细信息，请参阅[添加和管理实验室用户](how-to-configure-student-usage.md)。

## <a name="view-the-usage-of-vms-in-the-lab"></a>查看实验室中 VM 的使用情况 

1. 在左侧菜单中选择“虚拟机”。  
2. 确认可以看到 VM 的状态以及 VM 已运行的小时数。 实验室所有者在学生 VM 上花的时间不计入在最后一列中显示的使用时间。 

    ![VM 使用情况](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>管理学生 VM 
在此页上，你可以通过使用“状态”  列或工具栏上的控件来启动、停止或重置学生 VM。

![VM 控件](../media/tutorial-track-usage/vm-controls.png)

有关管理实验室虚拟计算机池的详细信息，请参阅[设置和管理虚拟计算机池](how-to-set-virtual-machine-passwords.md)。

## <a name="next-steps"></a>后续步骤
若要详细了解教室实验室，请查看[操作方法指南](how-to-manage-lab-accounts.md)中的文件。
