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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: a1a3e62646fedd468a02eac7b1a48d0b2d00fd74
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591976"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>教程：在 Azure 实验室服务中跟踪实验室使用情况
本教程介绍实验室创建者/所有者如何跟踪实验室使用情况。

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 查看注册到实验室的用户
> * 查看实验室中 VM 的使用情况
> * 管理学生 VM 


## <a name="view-registered-users"></a>查看已注册用户

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”  并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
3. 在“我的实验室”页上，选择要跟踪其使用情况的实验室。  
4. 选择左侧菜单上的“用户”，或选择“用户”磁贴  。  此时会看到注册到实验室的学生。  

    ![注册的用户](../media/tutorial-track-usage/registered-users.png)

    有关添加和管理实验室用户的详细信息，请参阅[添加和管理实验室用户](how-to-configure-student-usage.md)。

## <a name="view-the-usage-of-vms"></a>查看 VM 使用情况

1. 在左侧菜单中选择“虚拟机”。  
2. 确认可以看到 VM 的状态以及 VM 已运行的小时数。 实验室所有者在学生 VM 上花的时间不计入在最后一列中显示的使用时间。 

    ![VM 使用情况](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>管理学生 VM 
在此页上，你可以通过使用“状态”  列或工具栏上的控件来启动、停止或重置学生 VM。

![VM 操作](../media/tutorial-track-usage/vm-controls.png)

有关管理实验室虚拟机池的详细信息，请参阅[设置和管理虚拟机池](how-to-set-virtual-machine-passwords.md)。

> [!NOTE]
> 当教师打开学生 VM 时，该学生的配额不会受到影响。 用户配额指定用户在计划上课时间之外可用的实验室小时数。 有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="next-steps"></a>后续步骤
若要详细了解教室实验室，请查看[操作方法指南](how-to-manage-lab-accounts.md)中的文件。
