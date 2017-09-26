---
title: "管理 Azure 开发测试实验室中的实验室策略 | Microsoft 文档"
description: "了解如何定义实验室策略，例如 VM 大小、每个用户的最大VM 以及自动化关闭。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 328a4d893637d7150807855e118b485a2c3bbfc5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>管理 Azure 开发测试实验室中的某个实验室的所有策略

Azure 开发测试实验室允许通过管理每个实验室的策略（设置）来控制成本并尽量减少实验中的浪费。 本文将分步详细介绍如何设置每个策略。  

## <a name="set-allowed-virtual-machine-sizes"></a>设置允许的虚拟机大小
通过指定实验室中允许的 VM 大小，设置允许的 VM 大小策略有助于最小化实验室浪费。 若激活此策略，则只能按列表中的大小创建 VM。

1. 在实验室的“配置和策略”边栏选项卡上，选择“允许的虚拟机大小”。
   
    ![允许的虚拟机大小](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. 选择“开启”启用此策略，选择“关闭”禁用此策略。

1. 如果启用此策略，选择一个或多个可以在实验室中创建的 VM 大小。

1. 选择“保存”。

## <a name="set-virtual-machines-per-user"></a>设置每个用户的虚拟机
通过**每个用户的虚拟机**策略可指定个人用户可创建 VM 数量上限。 若用户在达到用户上限时尝试创建或声明 VM，则会出现错误消息，指出无法创建/声明 VM。 

1. 在实验室的“配置和策略”菜单中，选择“每个用户的虚拟机数”。
   
    ![每个用户的虚拟机](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. 选择“是”可限制每个用户的 VM 数。 如果不希望限制每个用户的 VM 数，请选择“否”。 如果选择“是”，请输入一个数字值，指示一个用户可创建或索取的 VM 数量上限。 

1. 选择“是”限制可以使用 SSD（固态磁盘）的 VM 数。 如果不希望限制可以使用 SSD 的 VM 数，请选择“否”。 如果选择“是”，请输入一个值，指示可以使用 SSD 创建的 VM 数量上限。 

1. 选择“保存”。

## <a name="set-virtual-machines-per-lab"></a>设置每个实验室的虚拟机
通过**每个实验室的虚拟机**策略可指定当前实验室可创建 VM 数量上限。 若用户在达到实验室上限时尝试创建 VM，则会出现错误消息，表示无法创建 VM。 

1. 在实验室的“配置和策略”菜单上，选择“每个实验室的虚拟机”。
   
    ![每个实验室的虚拟机](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. 选择“是”以限制每个实验室的 VM 数。 如果不希望限制每个实验室的 VM 数，则选择“否”。 如果选择了“是”，请输入一个数字值，指示一个用户可创建或索取的 VM 数量上限。 

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

    默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 若要从特定 VM 中删除此设置，请打开 VM 的边栏选项卡，然后更改其“自动关闭”设置 

## <a name="set-auto-start"></a>设置自动启动
通过自动启动策略可以指定启动当前实验室中虚 VM 的时间。  

1. 在实验室的“配置和策略”边栏选项卡上，选择“自动启动”。
   
    ![自动启动](./media/devtest-lab-set-lab-policy/auto-start.png)

2. 选择“开启”启用此策略，选择“关闭”禁用此策略。

3. 如果启用此策略，请指定计划的启动时间、时区以及在每周的哪几天应用该时间。 

4. 选择“保存”。

    一旦启用，此策略不会自动应用到当前实验室中所有 VM。 若要将设置应用到特定 VM，请打开 VM 的边栏选项卡，然后更改其“自动启动”设置 

## <a name="set-expiration-date"></a>设置到期日期
在[创建 VM](devtest-lab-add-vm.md) 时可以设置到期日期。 在“高级设置”中，选择日历图标，指定一个将自动删除 VM 的日期。  默认情况下，VM 永不过期。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
一旦定义并应用了各种实验室 VM 策略设置，以下为下一步尝试：

* [了解共享 IP 地址](devtest-lab-shared-ip.md) - 说明了在开发测试实验室中如何使用共享 IP 地址以最大程度地减少连接到你的实验室 VM 时所需的公共 IP 地址数。
* [配置成本管理](devtest-lab-configure-cost-management.md) - 演示了如何使用**每月的估计成本趋势**图表  
  查看当前所处月份的截止目前估计的成本以及截止本月结束预计成本。
* [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或 Marketplace 映像。 本文演示了如何从 VHD 文件创建自定义映像。
* [配置 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - Azure 开发测试实验室支持创建基于 Azure Marketplace 映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure Marketplace 映像（如果有）。
* [在实验室中创建 VM](devtest-lab-add-vm-with-artifacts.md) - 演示了如何从基本映像（自定义或 Marketplace 映像）创建 VM，以及如何使用在 VM 中使用项目。


