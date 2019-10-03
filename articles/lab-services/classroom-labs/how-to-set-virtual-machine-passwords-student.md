---
title: 在 Azure 实验室服务中重置课堂实验室 Vm 的密码 |Microsoft Docs
description: 了解如何在 Azure 实验室服务的教室实验室中重置虚拟机 (Vm) 的密码。
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
ms.openlocfilehash: 26b0f710590496875521e0dd8577a35841fbd3dd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657003"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>在课堂实验室中为虚拟机设置或重置密码 (学生)
本文介绍了学生如何为其 Vm 设置/重置密码。 

## <a name="enable-resetting-of-passwords"></a>启用密码重置
创建实验室时, 实验室所有者可以启用或禁用**所有虚拟机的相同密码**。 如果启用此选项, 则学生无法重置密码。 实验室中的所有 Vm 都具有与教师设置的密码相同的密码。 

如果禁用此选项, 则用户在首次尝试连接到 VM 时必须设置密码。 学生稍后还可以随时重置密码, 如本文最后一节所示。 

## <a name="reset-password-for-the-first-time"></a>首次重置密码
如果已禁用 "**为所有虚拟机使用同一密码**" 选项, 则当用户 (学生) 在 "**我的虚拟机**" 页上选择 "实验室" 磁贴上的 "**连接**" 按钮时, 用户将看到以下对话框为 VM 设置密码: 

![重置学生密码](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍后重置密码
学生还可以通过单击 "实验室" 磁贴上的溢出菜单 (**垂直三个点**), 然后选择 "**重置密码**" 来设置密码。 

![稍后重置密码](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>后续步骤
若要了解实验室所有者可以配置的其他学生使用选项, 请参阅以下文章:[配置学生使用情况](how-to-configure-student-usage.md)。
