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
ms.date: 10/18/2019
ms.author: spelluru
ms.openlocfilehash: 842392ab425628a1c82a39e25a65066064747211
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675769"
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

## <a name="view-the-usage-of-vms-in-the-lab"></a>查看实验室中 VM 的使用情况 

1. 在左侧菜单中选择“虚拟机”。  
2. 确认可以看到 VM 的状态以及 VM 已运行的小时数。 实验室所有者在学生 VM 上花的时间不计入在最后一列中显示的使用时间。 

    ![VM 使用情况](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>管理学生 VM 
在此页上，可以使用“状态”  列中的下拉列表或工具栏上的按钮来启动、停止或重置学生 VM。 

![VM 控件](../media/tutorial-track-usage/vm-controls.png)

也可使用工具栏按钮来启动、停止或删除 VM。 


## <a name="next-steps"></a>后续步骤
若要详细了解教室实验室，请查看[操作方法指南](how-to-manage-lab-accounts.md)中的文件。
