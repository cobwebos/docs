---
title: 在 Azure 开发测试实验室中为 VM 配置自动关闭设置
description: 了解如何为虚拟机（VM）配置自动关闭设置，以便 VM 在不使用时自动关闭。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 4cca4fd4bc9cd880c5b7a75e54e8cfd8192bae1e
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170194"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中为 VM 配置自动关闭设置
借助 Azure 开发测试实验室，可通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 本文介绍如何为实验室帐户配置自动关闭策略，并为实验室帐户中的实验室配置自动关闭设置。 若要了解如何设置每个实验室策略，请参阅[在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)。  

## <a name="autoshutdown-policy-for-a-lab"></a>实验室的自动关闭策略
实验室所有者可针对实验室中的所有 VM 配置关机计划。 这样可以节省成本，因为未使用（空闲）的计算机不会运行。 可以集中针对所有实验室 VM 实施关机策略，省得实验室用户付出精力针对每个计算机设置计划。 使用此功能可以针对实验室计划设置策略，一开始不为实验室用户提供任何控制权，逐渐为他们提供完全控制权。 实验室所有者可通过以下步骤配置此策略：

1. 在实验室的主页上，选择“配置和策略”。
2. 在左侧菜单的“计划”部分选择“自动关机策略”。
3. 选择一个选项。 以下部分更详细地介绍了这些选项：设置的策略只会应用到实验室中新建的 VM，而不会应用到现有的 VM。 

    ![自动关闭策略选项](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>为实验室配置自动关闭设置
通过允许你指定此实验室的 Vm 关闭的时间，自动关闭策略有助于最大程度地减少实验室浪费。

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需的实验室。   
4. 选择“配置和策略”。

    ![策略设置窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在实验室的 "**配置和策略**" 窗格中，选择 "**计划**" 下**的 "自动关闭**"。
   
    ![自动关闭](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 选择“开启”启用此策略，选择“关闭”禁用此策略。
7. 如果启用此策略，请指定要关闭当前实验室中所有 VM 的时间（和时区）。
8. 指定 **"是"** 或 "**否**" 以在指定的自动关闭时间前15分钟发送通知。 如果选择“是”，请输入 Webhook URL 终结点或电子邮件地址，指定要将通知发布或发送到的位置。 用户会收到通知，其中提供了延迟关闭的选项。 有关详细信息，请参阅 "[通知](#notifications)" 部分。 
9. 选择“保存”。

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 要从特定 VM 中删除此设置，请打开 VM 的管理窗格，然后更改其**自动关闭**设置。

## <a name="configure-autoshutdown-settings-for-a-vm"></a>为 VM 配置自动关闭设置

### <a name="user-sets-a-schedule-and-can-opt-out"></a>用户设置计划，并可选择禁用
如果为实验室设置了此策略选项，则用户可以替代或选择退出实验室计划。 此选项授予实验室用户对其 VM 的自动关机计划的完全控制权。 实验室用户在其 VM 自动关机计划页中看不到任何变化。

![自动关闭策略选项-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>用户设置计划，但无法选择禁用
如果为实验室设置了此策略选项，则用户可以覆盖实验室计划。 但是，他们无法选择禁用自动关机策略。 此选项可确保实验室中的每台计算机根据自动关机计划运行。 实验室用户可以更新其 VM 的自动关机计划，并设置关机通知。

![自动关闭策略选项-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>用户无法控制实验室管理员设置的计划
如果为实验室设置了此策略选项，则用户无法重写或选择退出实验室计划。 此选项可让实验室管理员全权控制实验室中每台计算机的计划。 实验室用户只能针对其 VM 设置自动关机通知。

![自动关闭策略选项-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>通知
一旦实验室所有者设置自动关闭，在其任何 Vm 将受到影响的情况下，在自动关闭触发之前，会向实验室用户发送通知。 使用此选项，实验室用户可以在关机前保存其工作。 此通知还会为每个 VM 提供以下操作的链接：

- 暂时跳过自动关闭
- 暂停自动关闭一小时或2小时，以便他们能够继续使用 VM。

通知将通过配置的 web 挂钩终结点或由实验室所有者在自动关闭设置中指定的电子邮件地址发送。 Webhook 允许生成或设置订阅特定事件的集成。 触发其中一个事件时，开发测试实验室会将 HTTP POST 有效负载发送到 webhook 的已配置 URL。 有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

建议你使用 web 挂钩，因为它们广泛受各种应用（例如，时差、Azure 逻辑应用等）的支持。并允许你实现自己的发送通知的方式。 有关使用 Azure 逻辑应用从电子邮件接收自动关闭通知的示例，请参阅[创建用于接收电子邮件通知的逻辑应用](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications)。 


## <a name="next-steps"></a>后续步骤
请参阅[管理 Azure 开发测试实验室中的实验室的自动关闭策略](devtest-lab-auto-shutdown.md)
