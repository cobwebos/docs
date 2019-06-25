---
title: 将密码设置为 Azure 实验室服务中的 Vm |Microsoft Docs
description: 了解如何设置和重置 Azure 实验室服务的教室实验室中的虚拟机 (Vm) 的密码。
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144107"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>设置或重置密码教室实验室中的虚拟机
本文提供不同的方法来设置和 resettings 密码用于在教室实验室中访问 Vm。 

## <a name="lab-owners-teachers"></a>实验室所有者 （教师）
实验室所有者 （教师） 可以设置/重置密码的 Vm 时创建实验室 （实验室创建向导） 或之后创建实验 （的仪表板）。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在创建实验室时设置密码
实验室所有者 （教师） 可以设置密码的 Vm 在实验室中对**设置凭据**实验室创建向导页。

![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)

通过启用/禁用**的所有虚拟机中使用相同的密码**选项在此页上，教师可以选择在实验室中的所有 Vm 使用相同的密码或允许学生将密码设置为其虚拟机。 默认情况下，除 Ubuntu 的所有 Windows 和 Linux 操作系统映像的情况下启用此设置。 禁用此设置后，将提示学生尝试首次连接到 VM 时设置密码。 

实验室所有者可以重置此密码 （如果需要） 上**配置模板**实验室创建向导页。 

![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

实验室所有者还可以重置密码后创建实验室后，在仪表板上。 

### <a name="reset-password-on-the-dashboard"></a>在仪表板上重置密码

1. 选择实验室磁贴，在溢出菜单 （三个垂直点），然后选择**重置密码**。 

    ![在主页页面菜单中重置密码](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 上**设置密码**对话框中，输入密码，然后选择**设置密码**。
    
    ![设置密码对话框](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>实验室用户 （学生）
创建实验室时，实验室所有者可以启用或禁用**的所有虚拟机中使用相同的密码**。 如果启用此选项，学生不能重置密码。 在实验室中的所有 Vm 将都具有相同的密码设置的教师。 

如果禁用此选项，则用户将必须尝试首次连接到 VM 时设置密码。 当用户 （学生） 选择**Connect**实验室磁贴上的按钮**我的虚拟机**页上，用户会看到以下对话框来设置 VM 密码： 

![学生的重置密码](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

学生还可以设置密码通过单击在溢出菜单 (**垂直的三个点**) 上的实验室磁贴，并选择**重置密码**。 

## <a name="next-steps"></a>后续步骤
若要详细了解其他学生使用情况选项您 （作为实验室所有者） 可以配置，请参阅以下文章：[配置学生使用](how-to-configure-student-usage.md)。
