---
title: 在 Azure 实验室服务中设置 Vm 的密码 |Microsoft Docs
description: 了解如何在 Azure 实验室服务的教室实验室中为虚拟机 (Vm) 设置和重置密码。
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645018"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>在课堂实验室 (讲师) 中为虚拟机设置或重置密码
实验室所有者 (教师) 可以在创建实验室 (实验室创建向导) 时或创建实验室之后 (在仪表板上) 为 Vm 设置/重置密码。 

## <a name="set-password-at-the-time-of-lab-creation"></a>在创建实验室时设置密码
实验室所有者 (教师) 可以在实验室创建向导的 "**设置凭据**" 页上, 在实验室中设置 vm 的密码。

![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)

通过在此页上启用/禁用 "**为所有虚拟机使用相同的密码**" 选项, 教师可以选择对实验室中的所有 vm 使用相同的密码, 或允许学生为其 vm 设置密码。 默认情况下, 对于所有 Windows 和 Linux 操作系统映像 (Ubuntu 除外), 将启用此设置。 如果禁用此设置, 则当学生首次尝试连接到 VM 时, 系统将提示用户设置密码。 

实验室所有者可以在 "实验室创建向导" 的 "**配置模板**" 页上重置此密码 (如果需要)。 

![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

在仪表板上创建实验室后, 实验室所有者还可以重置密码。 

## <a name="reset-password-on-the-dashboard"></a>在仪表板上重置密码

1. 选择 "实验室" 磁贴上的溢出菜单 (垂直三个点), 然后选择 "**重置密码**"。 

    ![主页上的 "重置密码" 菜单](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 在 "**设置密码**" 对话框中, 输入密码, 然后选择 "**设置密码**"。
    
    !["设置密码" 对话框](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>后续步骤
若要了解你 (作为实验室所有者) 可以配置的其他学生使用条款选项, 请参阅以下文章:[配置学生使用情况](how-to-configure-student-usage.md)。

若要了解学生如何重置其 Vm 的密码, 请参阅[在课堂实验室中为虚拟机设置或重置密码 (学生)](how-to-set-virtual-machine-passwords-student.md)。