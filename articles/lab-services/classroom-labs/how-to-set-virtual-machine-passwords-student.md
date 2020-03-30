---
title: 在 Azure 实验室服务中重置教室实验室 VM 的密码 |微软文档
description: 了解如何在 Azure 实验室服务的教室实验室中重置虚拟机 （VM） 的密码。
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
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583698"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>设置或重置教室实验室（学生）中的虚拟机密码
本文介绍学生如何为其 VM 设置/重置密码。 

## <a name="enable-resetting-of-passwords"></a>启用重置密码
在创建实验室时，实验室所有者可以为所有虚拟机启用或禁用**使用相同的密码**。 如果启用此选项，学生无法重置密码。 实验室中的所有 VM 都将具有讲师设置的相同密码。 

如果禁用此选项，用户在首次尝试连接到 VM 时必须设置密码。 学生还可以稍后随时重置密码，如本文最后一节所示。 

## <a name="reset-password-for-the-first-time"></a>首次重置密码
如果禁用了 **"对所有虚拟机使用相同的密码"** 选项，当用户（学生）选择 **"我的虚拟机**"页上实验室磁贴上的 **"连接**"按钮时，用户将看到以下对话框来设置 VM 的密码： 

![重置学生密码](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍后重置密码
学生还可以通过单击实验室磁贴上的溢出菜单（**垂直三个点**）并选择 **"重置密码**"来设置密码。 

![稍后重置密码](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>后续步骤
要了解实验室所有者可以配置的其他学生使用选项，请参阅以下文章：[配置学生使用情况](how-to-configure-student-usage.md)。
