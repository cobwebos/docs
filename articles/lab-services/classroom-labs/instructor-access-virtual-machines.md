---
title: 访问 Azure 实验室服务中的 VM 的讲师
description: 本文演示如何教师从讲师视图中访问其学生 VM。 例如，助教可以是一个班级的讲师，也可以是其他班级的学生。
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641935"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>从讲师视图中以学生身份访问虚拟机
本文介绍教师如何访问他们的 VM 参加他们作为学生参加的课程。 

下面是一个此功能将提供帮助的方案。 助教是一个班级的教师，但其他班的学生。 而且，教师希望从显示自己拥有的实验室的讲师视图中查看和访问学生 VM。 

## <a name="access-vms-from-instructor-view"></a>从讲师视图访问 VM

1. 登录到 Azure[实验室服务网站](https://labs.azure.com)。 你看到你拥有的实验室 这些实验室可能是您自己创建的实验室，也可以是管理员指派为您分配的实验室的所有者。 有关详细信息，请参阅[如何向现有实验室添加其他所有者](how-to-add-user-lab-owner.md)
2. 要访问您作为学生参加的班级的 VM，请选择右上角的计算机图标。 确认您看到可作为学生访问的 VM。 在下面的示例中，用户是 Python 实验室的助教，但也是 Java 实验室的学生。 因此，用户在下拉列表中看到来自 Java 实验室的 VM。 用户可以启动 VM 并连接到它。 
    
    ![访问学生 VM](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [连接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-rdp.md)
- [将远程桌面用于 Linux 虚拟机](how-to-use-remote-desktop-linux-student.md)
