---
title: 使用 Azure Policy 在创建 VM 时自动启用备份
description: 本文介绍如何使用 Azure Policy 为在给定范围内创建的所有 VM 自动启用备份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: fd54c25899bc1e58a753821d2c7c2df8ff213865
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763586"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure Policy 在创建 VM 时自动启用备份

在一个组织中，备份或法规符合性管理员的主要职责之一是确保所有业务关键型计算机都以适当的保留期进行备份。

目前，Azure 备份提供了一个内置策略（使用 Azure Policy），可以将其分配给“订阅或资源组中指定位置的所有 Azure VM”。 将此策略分配到给定范围时，该范围中创建的所有新 VM 都将自动配置为备份到位于“同一位置和订阅中的现有保管库”。 用户可以指定保管库和备份 Vm 应关联到的保留策略。

## <a name="supported-scenarios"></a>支持的方案

* 内置策略当前仅支持 Azure VM。 用户必须确保分配期间指定的保留策略是 VM 保留策略。 请参阅 [本](./backup-azure-policy-supported-skus.md) 文档以查看此策略支持的所有 VM sku。

* 策略一次可以分配给一个位置和订阅。 若要跨位置和订阅启用 VM 备份，需要创建策略分配的多个实例，位置和订阅的每个组合都需要创建一个实例。

* 指定的保管库和为备份配置的 VM 可以位于不同的资源组下。

* 当前不支持管理组范围。

* 内置策略当前在国家/地区云中不可用。

## <a name="using-the-built-in-policy"></a>使用内置策略

若要将策略分配到所需的范围，请执行以下步骤：

1. 登录到 Azure 门户并导航到“策略”仪表板。
1. 在左边的菜单中选择“定义”以获取跨 Azure 资源的所有内置策略的列表。
1. 在列表中筛选“类别=备份”的项。 你会看到筛选列表，其中列出了名为 "在同一位置的现有中央保管库的 Vm 上配置备份" 的单个策略。
![Policy 仪表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. 选择策略的名称。 你将被重定向到此策略的详细定义。
![策略定义窗格](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. 选择窗格顶部的 " **分配** " 按钮。 这会将你重定向到 " **分配策略** " 窗格。
1. 在 " **基本**信息" 下，选择 " **作用域** " 字段旁边的三个点。 这会打开一个正确的上下文窗格，您可以在其中选择要应用策略的订阅。 还可以选择资源组，使该策略仅应用于特定资源组中的 VM。
![策略分配基础知识](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. 在“参数”选项卡中，从下拉列表中选择一个位置，然后选择范围中的 VM 必须关联的保管库和备份策略。
![策略分配参数](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. 确保将“效果”设置为 deployIfNotExists。
1. 导航到 " **查看 + 创建** " 并选择 " **创建**"。

> [!NOTE]
>
> 也可以通过使用[修正](../governance/policy/how-to/remediate-resources.md)，在现有 VM 上使用 Azure Policy。

> [!NOTE]
>
> 建议不要一次将此策略分配给超过 200 个 VM。 如果将此策略分配给超过 200 个 VM，则可能导致备份触发时间比计划指定的时间晚几个小时。

## <a name="next-steps"></a>后续步骤

[了解有关 Azure Policy 的详细信息](../governance/policy/overview.md)
