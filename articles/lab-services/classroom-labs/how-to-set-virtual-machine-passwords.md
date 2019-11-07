---
title: 在 Azure 实验室服务中设置 Vm 的密码 |Microsoft Docs
description: 了解如何在 Azure 实验室服务的教室实验室中为虚拟机（Vm）设置和重置密码。
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d89d506d8912706bbdb802801b16d01036ecb8e2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583492"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>设置和管理虚拟机池 
本文介绍如何执行以下任务：

- 增加实验室中虚拟机（Vm）的数量
- 启动所有 Vm 或所选 Vm 
- 重置 Vm

## <a name="update-the-lab-capacity"></a>更新实验室容量
若要增加或减少实验室容量（实验室中虚拟机的数量），请执行以下步骤：

1. 在 "**虚拟机池**" 页上，选择 "**实验室容量： &lt;号&gt; 计算机**"。
2. 在实验室中输入所需的新**虚拟机数**。 此数字必须大于或等于实验室中注册的用户数。 
3. 然后选择“保存”。 

    ![全部启动按钮](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果增加了容量，则可以看到正在创建的 VM。 如果在列表中看不到新 VM，请刷新页面。 

    ![正在创建 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>启动 VM

### <a name="start-ot-stop-all-vms"></a>启动停止所有 Vm
1. 切换到 "**虚拟机池**" 页。 
2. 从工具栏中选择 "**全部启动**"。 

    ![全部启动按钮](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 所有 Vm 启动后，可以通过选择工具栏上的 "**全部停止**" 按钮来停止所有 vm。 

    ![全部停止按钮](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>启动所选 Vm
可以通过两种方式启动选定的 Vm （一个或多个）。 第一种方法是在列表中选择 VM 或 Vm，然后在工具栏上选择 "**启动**"。 

第二种方法是在列表中选择一个或多个 Vm，并切换 "**状态**" 列中的按钮。 

![启动所选 Vm](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同样，可以通过切换 "**状态**" 列中的按钮或在工具栏上选择 "**停止**" 来停止一个或多个 vm。 

## <a name="reset-vms"></a>重置 Vm
要重置一个或多个 Vm，请在列表中将其选中，然后在工具栏上选择 "**重置**"。 

![重置所选 Vm](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在 "**重置虚拟机**" 对话框中，选择 "**重置**"。 

!["重置 VM" 对话框](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>设置 Vm 的密码
实验室所有者（教师）可以在创建实验室（实验室创建向导）时或在**模板**页上创建实验室后，为 vm 设置/重置密码。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在创建实验室时设置密码
实验室所有者（教师）可以在实验室创建向导的 "**虚拟机凭据**" 页上的实验室中设置 vm 的密码。

![新建实验室窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

通过在此页上启用/禁用 "**为所有虚拟机使用相同的密码**" 选项，教师可以选择对实验室中的所有 vm 使用相同的密码，或允许学生为其 vm 设置密码。 默认情况下，对于所有 Windows 和 Linux 操作系统映像（Ubuntu 除外），将启用此设置。 如果禁用此设置，则当学生首次尝试连接到 VM 时，系统将提示用户设置密码。 

### <a name="reset-password-later"></a>稍后重置密码

1. 在实验室的 "**模板**" 页上，选择工具栏上的 "**重置密码**"。 
1. 在 "**重置密码**" 对话框中，输入密码，然后选择 "**重置密码**"。
    
    !["设置密码" 对话框](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>连接到学生 Vm
如果满足以下条件，实验室创建者（讲师/教授）可以连接到学生 VM： 

- 创建实验室时选择了 "**为所有虚拟机使用相同的密码**" 选项
- VM 正在运行 

 若要连接到 student VM，请将鼠标悬停在 VM 上的列表中，然后选择 "计算机" 按钮。  

!["连接到学生 VM" 按钮](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

## <a name="next-steps"></a>后续步骤
若要了解你（作为实验室所有者）可以配置的其他学生使用条款选项，请参阅以下文章：[配置学生使用情况](how-to-configure-student-usage.md)。

若要了解学生如何重置其 Vm 的密码，请参阅[在课堂实验室中为虚拟机设置或重置密码（学生）](how-to-set-virtual-machine-passwords-student.md)。