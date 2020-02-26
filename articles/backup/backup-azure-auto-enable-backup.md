---
title: 使用 Azure Policy 在创建 VM 时自动启用备份
description: 本文介绍如何使用 Azure 策略为在给定作用域中创建的所有 Vm 自动启用备份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77584262"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure Policy 在创建 VM 时自动启用备份

组织中的备份或合规性管理员的主要职责之一是确保使用适当的保留来备份所有关键业务计算机。

目前，Azure 备份提供了一个内置策略（使用 Azure 策略），可以将其分配给**订阅或资源组中指定位置的所有 Azure vm**。 将此策略分配到给定作用域时，会自动将该作用域中创建的所有新 Vm 配置为备份到**同一位置和订阅中的现有保管库**。 用户可以指定保管库和备份 Vm 应关联到的保留策略。

## <a name="supported-scenarios"></a>支持的方案

* 目前只有 Azure Vm 支持内置策略。 用户必须小心，以确保在分配过程中指定的保留策略为 VM 保留策略。 请参阅[本](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus)文档以查看此策略支持的所有 VM sku。

* 可以一次将策略分配到一个位置和订阅。 若要跨位置和订阅启用 Vm 备份，需要创建策略分配的多个实例，每个实例都适用于位置和订阅的每个组合。

* 指定的保管库和为备份配置的 Vm 可以在不同的资源组下。

* 管理组作用域目前不受支持。

* 内置策略当前在国家云中不可用。

## <a name="using-the-built-in-policy"></a>使用内置策略

若要将策略分配到所需的范围，请执行以下步骤：

1. 登录到 Azure 门户，并导航到 "**策略**" 仪表板。
2. 选择左侧菜单中的 "**定义**" 可获取跨 Azure 资源的所有内置策略的列表。
3. 筛选 "**类别 = 备份**" 列表。 你会看到筛选列表，其中列出了名为 "在同一位置的现有中央保管库的 Vm 上配置备份" 的单个策略。
![策略仪表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 单击该策略的名称。 你将被重定向到此策略的详细定义。
![策略定义 "边栏选项卡](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 单击边栏选项卡顶部的 "**分配**" 按钮。 这会将你重定向到 "**分配策略**" 边栏选项卡。
6. 在 "**基本**信息" 下，单击 "**作用域**" 字段旁边的三个点。 这会打开一个正确的上下文边栏选项卡，你可以在其中选择要应用策略的订阅。 你还可以选择选择一个资源组，以便仅将该策略应用于特定资源组中的 Vm。
![策略分配基础知识](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在 "**参数**" 选项卡中，从下拉选项中选择一个位置，然后选择必须与作用域中的 vm 关联的保管库和备份策略。
![策略分配参数](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. 确保将 "**效果**" 设置为 "deployIfNotExists"。
9. 导航到 "**查看 + 创建**"，然后单击 "**创建**"。

> [!NOTE]
>
> Azure 策略还可以在现有 Vm 上使用[修正](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)。

> [!NOTE]
>
> 建议一次不将此策略分配到200个以上的 Vm。 如果将策略分配给超过200个 Vm，则可能会导致在数小时后触发备份，超过计划指定的时间。

## <a name="next-steps"></a>后续步骤

[了解有关 Azure 策略的详细信息](https://docs.microsoft.com/azure/governance/policy/overview)
