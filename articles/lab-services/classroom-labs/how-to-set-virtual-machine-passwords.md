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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123228"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>设置或重置密码教室实验室中的虚拟机
本文提供不同的方法来设置和 resettings 密码用于在教室实验室中访问 Vm。 

## <a name="lab-owners-teachers"></a>实验室所有者 （教师）
实验室所有者 （教师） 可以设置密码的 Vm 在实验室中对**设置凭据**实验室创建向导页。

![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)

然后，实验室所有者可以重置密码 （如果需要） 上**配置模板**实验室创建向导页。 

![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

实验室所有者还可以重置密码后创建实验室后，在仪表板上。 

![在主页页面菜单中重置密码](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

然后，输入中的新密码**设置密码**页，然后选择**设置密码**。 

![在主页页面菜单中重置密码](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>实验室用户 （学生）
在创建实验室的时间。 同时，实验室所有者可以启用或禁用允许学生 Vm 设置自己的密码。 如果实验室所有者 （教师） 启用此选项，实验室用户 （学生） 可以选择为 VM 设置密码时该学生登录到 VM 的第一次。 上**Windows VM**，按**CTRL + ALT + 最终**，然后选择**更改密码**。 

## <a name="next-steps"></a>后续步骤
若要详细了解其他学生使用情况选项您 （作为实验室所有者） 可以配置，请参阅以下文章：[配置学生使用](how-to-configure-student-usage.md)。
