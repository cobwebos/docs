---
title: 在 Azure 实验室服务中设置 VM 密码 | Microsoft Docs
description: 了解如何在 Azure 实验室服务的课堂实验室中为虚拟机 (VM) 设置和重置密码。
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
ms.openlocfilehash: 8479c00ed2129d5726421f576e1fffdb4506fb4b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592468"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>设置和管理虚拟机池 
本文演示如何完成以下任务：

- 增加实验室中虚拟机 (VM) 的数目
- 启动所有 VM 或选定的 VM 
- 重置 VM

## <a name="update-the-lab-capacity"></a>更新实验室容量
若要增加或减少实验室容量（实验室中虚拟机的数量），请执行以下步骤：

1. 在“虚拟机池”页上，选择“实验室容量:&lt;数量&gt;台虚拟机”。
2. 输入实验室中所需的新“VM 数”。 此数字必须大于或等于实验室中注册的用户数。 
3. 然后选择“保存”。 

    ![“全部启动”按钮](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果增加了容量，则可以看到正在创建的 VM。 如果在列表中看不到新 VM，请刷新页面。 

    ![正在创建 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>启动 VM

### <a name="start-ot-stop-all-vms"></a>启动或停止所有 VM
1. 切换到“虚拟机池”页。 
2. 从工具栏中选择“全部启动”。 

    ![“全部启动”按钮](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 在所有 VM 启动后，可以通过选择工具栏上的“全部停止”按钮来停止所有 VM。 

    ![“全部停止”按钮](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>启动选定的 VM
可通过两种方式启动选定的 VM（一个或多个）。 第一种方法是在列表中选择一个或多个 VM，然后在工具栏上选择“启动”。 

第二种方法是在列表中选择一个或多个 VM，并在“状态”列中切换按钮。 

![启动选定的 VM](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同样，可以通过以下方式停止一个或多个 VM：在“状态”列中切换按钮，或在工具栏上选择“停止”。 

> [!NOTE]
> 当教师打开学生 VM 时，该学生的配额不会受到影响。 用户配额指定用户在计划上课时间之外可用的实验室小时数。 有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="reset-vms"></a>重置 VM
要重置一个或多个 VM，请在列表中将其选中，然后在工具栏上选择“重置”。 

![重置选定的 VM](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在“重置虚拟机”对话框中，选择“重置”。 

![“重置 VM”对话框](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>为 VM 设置密码
实验室所有者（教师）可以在创建实验室（实验室创建向导）时或在“模板”页上创建实验室后，为 VM 设置/重置密码。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在创建实验室时设置密码
实验室所有者（教师）可以在实验室创建向导的“虚拟机凭据”页上，为实验室中 VM 设置密码。

![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

通过启用/禁用此页上的“为所有虚拟机使用相同密码”选项，教师可以选择对实验室中的所有 VM 使用相同密码，或允许学生为其 VM 设置密码。 默认情况下，所有 Windows 和 Linux 操作系统映像（Ubuntu 除外）均启用此设置。 禁用此设置时，在学生尝试首次连接到 VM 时，会提示他们设置密码。 

### <a name="reset-password-later"></a>稍后重置密码

1. 在实验室的“模板”页上，选择工具栏上的“重置密码”。 
1. 在“重置密码”对话框中，输入密码，然后选择“重置密码”。
    
    ![“设置密码”对话框](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>连接到学生 VM
如果满足以下条件，实验室创建者（教师）可以连接到学生 VM： 

- 创建实验室时选择“为所有虚拟机使用相同密码”选项
- VM 正在运行 

 若要连接到学生 VM，请将鼠标悬停在列表中的 VM 上，然后选择“虚拟机”按钮。  

![连接到学生 VM 按钮](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 当教师启动 VM 并连接到该 VM 时，学生配额不受影响。 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>将虚拟机列表导出到 CSV 文件

1. 切换到“虚拟机池”选项卡。
2. 选择工具栏上的“...”（省略号），然后选择“导出 CSV”。 

    ![导出虚拟机列表](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>后续步骤
若要了解你（作为实验室所有者）可以配置的其他学生使用选项，请参阅以下文章：[学生使用配置](how-to-configure-student-usage.md)。

若要了解学生如何重置其 VM 密码，请参阅[为课堂实验室中的虚拟机设置或重置密码（学生）](how-to-set-virtual-machine-passwords-student.md)。