---
title: "管理 Azure 开发测试实验室中的基本实验室策略 | Microsoft 文档"
description: "了解如何针对开发测试实验室中的实验室设置一些基本策略（设置）"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: tarcher
ms.openlocfilehash: ed35d081b191ec41ed9e5970515057a4715c0d59
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>管理 Azure 开发测试实验室中某个实验室的基本策略

借助 Azure 开发测试实验室，可通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 在本文中，你开始使用策略通过学习如何设置两个最严重策略-限制可以创建或单个用户，占用的虚拟机 (VM) 的数量和配置自动关闭。 若要了解如何设置每个实验室策略，请参阅 [在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)一文。  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>访问 Azure 开发测试实验室中的实验室策略
以下步骤指导完成 Azure 开发测试实验室中的实验室策略设置：

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需实验室。   

1. 选择“配置和策略”。

    ![策略设置边栏选项卡](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. “配置和策略”边栏选项卡包含可以指定的设置的菜单。 本文仅介绍“每个用户的虚拟机数”和“自动关闭”设置。 若要了解剩余的设置，请参阅[管理 Azure 开发测试实验室中某个实验室的所有策略](./devtest-lab-set-lab-policy.md)。 
   
## <a name="set-virtual-machines-per-user"></a>设置每个用户的虚拟机
通过**每个用户的虚拟机**策略可指定个人用户可创建 VM 数量上限。 若用户在达到用户上限时尝试创建或声明 VM，则会出现错误消息，指出无法创建/声明 VM。 

1. 在实验室的“配置和策略”菜单中，选择“每个用户的虚拟机数”。
   
    ![每个用户的虚拟机](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. 选择“是”可限制每个用户的 VM 数。 如果不希望限制每个用户的 VM 数，请选择“否”。 如果选择“是”，请输入一个数字值，指示一个用户可创建或索取的 VM 数量上限。 

1. 选择“是”限制可以使用 SSD（固态磁盘）的 VM 数。 如果不希望限制可以使用 SSD 的 VM 数，请选择“否”。 如果选择“是”，请输入一个值，指示可以使用 SSD 创建的 VM 数量上限。 

1. 选择“保存”。

## <a name="set-auto-shutdown"></a>设置自动关闭
通过指定此实验室 VM 自动关闭的时间，自动关闭策略有助于最小化实验室浪费。

1. 在实验室的“配置和策略”边栏选项卡中，选择“自动关闭”。
   
    ![自动关闭](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. 选择“开启”启用此策略，选择“关闭”禁用此策略。

1. 如果启用此策略，请指定要关闭当前实验室中所有 VM 的时间（和时区）。

1. 对于在指定的自动关闭时间之前 15 分钟发送通知的选项，指定“是”或“否”。 如果指定“是”，请输入要接收通知的 Webhook URL 终结点。 有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

1. 选择“保存”。

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 要从特定 VM 中删除此设置，请打开 VM 的边栏选项卡，并更改其“自动关闭”设置 

## <a name="set-auto-start"></a>设置自动启动
通过自动启动策略可以指定启动当前实验室中虚 VM 的时间。  

1. 在实验室的“配置和策略”边栏选项卡上，选择“自动启动”。
   
    ![自动启动](./media/devtest-lab-set-lab-policy/auto-start.png)

2. 选择“开启”启用此策略，选择“关闭”禁用此策略。

3. 如果启用此策略，请指定计划的启动时间、时区以及在每周的哪几天应用该时间。 

4. 选择“保存”。

    一旦启用，此策略不会自动应用到当前实验室中所有 VM。 要将设置应用到特定 VM，请打开 VM 的边栏选项卡，然后更改其“自动启动”设置 

## <a name="next-steps"></a>后续步骤

- [在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md) - 了解如何修改其他实验室策略 
