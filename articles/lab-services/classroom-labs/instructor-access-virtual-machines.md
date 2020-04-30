---
title: 讲师访问 Azure 实验室服务中的 Vm
description: 本文介绍了讲师如何从讲师视图访问学生 Vm。 例如，教学助手可以是一个类的指导员，而是其他类的学生。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641935"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>从讲师视图以学生身份访问虚拟机
本文说明了教师如何为作为学生参与的类访问其 Vm。 

下面是此功能的一个方案。 教授助手是一个类的指导员，而是其他类中的一个学生。 而且，教学讲师希望查看和访问讲师视图中的学生 Vm，其中显示了他们拥有的实验室。 

## <a name="access-vms-from-instructor-view"></a>从讲师视图访问 Vm

1. 登录到[Azure 实验室服务网站](https://labs.azure.com)。 你会看到你拥有的实验室。 这些实验室可以是自己创建的实验室，也可以是管理员分配给您的作为所有者的实验室。 有关详细信息，请参阅[如何将其他所有者添加到现有实验室](how-to-add-user-lab-owner.md)
2. 若要访问作为学生参与的类的 Vm，请选择右上角的 "计算机" 图标。 确认你看到可以作为学生访问的 Vm。 在下面的示例中，用户是 Python 实验室的教授助手，而是 Java 实验室的学生。 因此，用户在下拉列表中从 Java 实验室查看 VM。 用户可以启动 VM 并连接到该 VM。 
    
    ![访问学生 Vm](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [连接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-rdp.md)
- [使用适用于 Linux 虚拟机的远程桌面](how-to-use-remote-desktop-linux-student.md)
