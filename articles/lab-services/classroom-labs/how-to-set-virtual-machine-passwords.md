---
title: 为 Azure 实验室服务中的 VM 设置密码 |微软文档
description: 了解如何在 Azure 实验室服务的教室实验室中设置和重置虚拟机 （VM） 的密码。
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933799"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>设置和管理虚拟机池 
本文介绍如何执行以下任务：

- 增加实验室中的虚拟机 （VM） 数量
- 启动所有 VM 或所选 VM 
- 重置 VM

## <a name="update-the-lab-capacity"></a>更新实验室容量
要增加或减少实验室容量（实验室中的虚拟机数量），执行以下步骤：

1. 在**虚拟机池**页上，选择 **"实验室容量：&lt;数字&gt;计算机**"。
2. 在实验室中输入所需的新**VM 数量**。 此数字必须大于或等于实验室中注册的用户数。 
3. 然后，选择 **"保存**"。 

    ![启动所有按钮](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果增加了容量，可以看到正在创建的 VM 或 VM。 如果在列表中看不到新的 VM，请刷新页面。 

    ![正在创建的 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>启动 VM

### <a name="start-ot-stop-all-vms"></a>开始停止所有 VM
1. 切换到**虚拟机池**页面。 
2. 从工具栏**中选择"全部开始**"。 

    ![启动所有按钮](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 启动所有 VM 后，您可以通过选择工具栏上的"**停止所有"** 按钮来停止所有 VM。 

    ![停止所有按钮](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>启动所选 VM
有两种方法可以启动选定的 VM（一个或多个）。 第一种方法是在列表中选择 VM 或 VM，然后在工具栏上选择 **"开始"。** 

第二种方法是在列表中选择一个或多个 VM，并切换 **"状态"** 列中的按钮。 

![启动所选 VM](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同样，您可以通过切换 **"状态**"列中的按钮或选择工具栏上的 **"停止"** 来停止一个或多个 VM。 

> [!NOTE]
> 当教育者打开学生 VM 时，学生的配额不受影响。 用户的配额指定在计划类时间之外可供用户可用的实验室小时数。 有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="reset-vms"></a>重置 VM
要重置一个或多个 VM，请在列表中选择它们，然后在工具栏上选择 **"重置**"。 

![重置选定的 VM](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在 **"重置虚拟机"对话框中**，选择 **"重置**"。 

![重置 VM 对话框](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>为 VM 设置密码
实验室所有者（教师）可以在创建实验室时（实验室创建向导）或**在模板**页面上创建实验室后设置/重置 VM 的密码。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在实验室创建时设置密码
实验室所有者（教师）可以在实验室创建向导的**虚拟机凭据**页上为实验室中的 VM 设置密码。

![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

通过启用/禁用此页面**上所有虚拟机使用相同的密码**选项，教师可以选择对实验室中的所有 VM 使用相同的密码，或者允许学生为其 VM 设置密码。 默认情况下，此设置适用于除 Ubuntu 之外的所有 Windows 和 Linux 操作系统映像。 禁用此设置后，将提示学生在首次尝试连接到 VM 时设置密码。 

### <a name="reset-password-later"></a>稍后重置密码

1. 在实验室的**模板**页上，选择工具栏上的 **"重置密码**"。 
1. 在 **"重置密码"** 对话框中，输入密码，然后选择 **"重置密码**"。
    
    ![设置密码对话框](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>连接到学生 VM
如果满足以下条件，实验室创建者（讲师/教授）可以连接到学生 VM： 

- 创建实验室时选择了 **"对所有虚拟机使用相同的密码**"选项
- VM 正在运行 

 要连接到学生 VM，请将鼠标悬停在列表中的 VM 上，然后选择计算机按钮。  

![连接到学生 VM 按钮](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 当教授启动 VM 并连接到 VM 时，学生配额不受影响。 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>将虚拟机列表导出到 CSV 文件

1. 切换到**虚拟机池**选项卡。
2. 在工具栏上选择 **...（** 省略号），然后选择 **"导出 CSV**"。 

    ![虚拟机导出列表](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>后续步骤
要了解您（作为实验室所有者）可以配置的其他学生使用选项，请参阅以下文章：[配置学生使用情况](how-to-configure-student-usage.md)。

要了解学生如何重置 VM 的密码，请参阅[在教室实验室（学生）中设置或重置虚拟机的密码](how-to-set-virtual-machine-passwords-student.md)。