---
title: "在 Azure 开发测试实验室中定义实验室策略 | Microsoft Docs"
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
ms.date: 01/11/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f7656382500682898cd3ed6372630afa3c3f6350
ms.openlocfilehash: 2b16840e5e9fc6e0268d83f9177a6fc6ad02841c


---
# <a name="define-lab-policies-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中定义实验室策略
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

通过 Azure 开发测试实验室可指定主要策略，控制成本并将实验中的浪费最小化。 这些实验室策略包括每个用户和每个实验室可创建的 VM 数量上限，和何种自动关闭和自动启动选项。 

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>访问 Azure 开发测试实验室中的实验室策略
以下步骤将指导你完成 Azure 开发测试实验室中的实验室策略设置：

若要查看（和更改）实验室策略，请按以下步骤操作：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需实验室。   
4. 选择“策略设置”。
5. “策略设置”边栏选项卡中包含可选择的设置菜单： 
   
    ![策略设置边栏选项卡](./media/devtest-lab-set-lab-policy/policies.png)
   
    若要了解有关设置策略的详细信息，请从以下列表中选择：
   
   * [允许虚拟机大小](#set-allowed-virtual-machine-sizes) - 选择实验室中允许的虚拟机大小列表。 用户仅能从该列表创建虚拟机。
   * [每个用户的虚拟机](#set-virtual-machines-per-user) - 指定用户可创建 VM 数量上限。 
   * [每个实验室的虚拟机](#set-virtual-machines-per-lab) - 指定实验室可创建 VM 数量上限。 
   * [自动关闭](#set-auto-shutdown) - 指定当前实验室 VM 自动关闭的时间。
   * [自动启动](#set-auto-start) - 指定当前实验室 VM 自动开启的时间。

## <a name="set-allowed-virtual-machine-sizes"></a>设置允许的虚拟机大小
通过指定实验室中允许的 VM 大小，设置允许的 VM 大小策略有助于最小化实验室浪费。 若激活此策略，则只能按列表中的大小创建 VM。

1. 在实验的“策略设置”边栏选项卡，选择“允许的虚拟机大小”。
   
    ![允许的虚拟机大小](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
2. 选择“开启”启用此策略，选择“关闭”禁用此策略。
3. 如果启用此策略，选择一个或多个可以在实验室中创建的 VM 大小。
4. 选择“保存”。

## <a name="set-virtual-machines-per-user"></a>设置每个用户的虚拟机
通过**每个用户的虚拟机**策略可指定个人用户可创建 VM 数量上限。 若用户在达到用户上限时尝试创建 VM，则会出现错误消息，表示无法创建 VM。 

1. 在实验的“策略设置”边栏选项卡，选择“每个用户的虚拟机”。
   
    ![每个用户的虚拟机](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)
2. 选择“开启”启用此策略，选择“关闭”禁用此策略。
3. 如果启用此策略，请输入一个数字值，表示一个用户可创建的 VM 数量上限。 
   如果输入的数字无效，则 UI 会显示此字段允许的最大数。
4. 选择“保存”。

## <a name="set-virtual-machines-per-lab"></a>设置每个实验室的虚拟机
通过**每个实验室的虚拟机**策略可指定当前实验室可创建 VM 数量上限。 若用户在达到实验室上限时尝试创建 VM，则会出现错误消息，表示无法创建 VM。 

1. 在实验的“策略设置”边栏选项卡，选择“每个实验室的虚拟机”。
   
    ![每个实验室的虚拟机](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)
2. 选择“开启”启用此策略，选择“关闭”禁用此策略。
3. 如果启用此策略，请输入一个数字值，表示当前实验室可创建的 VM 数量上限。 
   如果输入的数字无效，则 UI 会显示此字段允许的最大数。
4. 选择“保存”。

## <a name="set-auto-shutdown"></a>设置自动关闭
通过指定此实验室 VM 自动关闭的时间，自动关闭策略有助于最小化实验室浪费。

1. 在实验的“策略设置”边栏选项卡，选择“自动关闭”。
   
    ![自动关闭](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
2. 选择“开启”启用此策略，选择“关闭”禁用此策略。
3. 如果启用此策略时，指定关闭当前实验室中所有 VM 的本地时间。
4. 选择“保存”。
5. 默认情况下，一旦启用，此策略会应用到当前实验室中所有 VM。 若要从特定 VM 中删除此设置，请打开 VM 的边栏选项卡，然后更改其“自动关闭”设置 

## <a name="set-auto-start"></a>设置自动启动
通过自动启动策略可以指定启动当前实验室中虚 VM 的时间。  

1. 在实验的“策略设置”边栏选项卡，选择“自动启动”。
   
    ![自动启动](./media/devtest-lab-set-lab-policy/auto-start.png)
2. 选择“开启”启用此策略，选择“关闭”禁用此策略。
3. 如果启用此策略，指定计划的本地启动时间以及每周应用策略的日期。 
4. 选择“保存”。
5. 一旦启用，此策略不会自动应用到当前实验室中所有 VM。 若要将设置应用到特定 VM，请打开 VM 的边栏选项卡，然后更改其“自动启动”设置 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
一旦定义并应用了各种实验室 VM 策略设置，以下为下一步尝试：

* [配置成本管理](devtest-lab-configure-cost-management.md) - 演示了如何使用**每月的估计成本趋势**图表  
  查看当前所处月份的截止目前估计的成本以及截止本月结束预计成本。
* [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或应用商店映像。 本文演示了如何从 VHD 文件创建自定义映像。
* [配置应用商店映像](devtest-lab-configure-marketplace-images.md) - Azure 开发测试实验室支持创建基于 Azure 应用商店映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure 应用商店映像（如果有）。
* [在实验室中创建 VM](devtest-lab-add-vm-with-artifacts.md) - 演示了如何从基本映像（自定义或应用商店映像）创建 VM，以及如何使用在 VM 中使用项目。




<!--HONumber=Jan17_HO2-->


