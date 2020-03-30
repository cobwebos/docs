---
title: 使用 Azure Policy 在创建 VM 时自动启用备份
description: 描述如何使用 Azure 策略自动启用给定作用域中创建的所有 VM 的备份的文章
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584262"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure Policy 在创建 VM 时自动启用备份

组织中备份或合规性管理员的主要职责之一是确保所有业务关键型计算机都具有适当的保留性。

如今，Azure 备份提供了一个内置策略（使用 Azure 策略），可以分配给**订阅或资源组中指定位置中的所有 Azure VM。** 当此策略分配给给定作用域时，该作用域中创建的所有新 VM 将自动配置为备份到**同一位置和订阅中的现有保管库**。 用户可以指定备份 VM 应关联的保管库和保留策略。

## <a name="supported-scenarios"></a>支持的方案

* 内置策略当前仅支持 Azure VM。 用户必须注意确保分配期间指定的保留策略是 VM 保留策略。 请参阅[本文档](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus)以查看此策略支持的所有 VM SKU。

* 策略可以一次分配给单个位置和订阅。 为了跨位置和订阅为 VM 启用备份，需要创建策略分配的多个实例，每个位置和订阅组合一个实例。

* 为备份配置的指定保管库和 VM 可以属于不同的资源组。

* 管理组作用域当前不受支持。

* 内置策略目前不在国家云中。

## <a name="using-the-built-in-policy"></a>使用内置策略

要将策略分配给所需的范围，请按照以下步骤操作：

1. 登录到 Azure 门户并导航到**策略**仪表板。
2. 选择左侧菜单中**的定义**，以获取有关 Azure 资源的所有内置策略的列表。
3. 筛选**类别=备份**的列表。 您将看到列表被筛选为名为"将位置的 VM 上的备份配置为同一位置的现有中央保管库"的单个策略。
![策略仪表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 单击策略的名称。 您将被重定向到此策略的详细定义。
![策略定义边栏选项卡](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 单击边栏选项卡顶部的 **"分配"** 按钮。 这将将您重定向到 **"分配策略"** 边栏选项卡。
6. 在 **"基础知识"** 下，单击 **"范围"** 字段旁边的三个点。 这将打开一个正确的上下文边栏选项卡，您可以在其中选择要应用的策略的订阅。 您还可以选择资源组，以便策略仅应用于特定资源组中的 VM。
![策略分配基础知识](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在 **"参数"** 选项卡中，从下拉列表中选择一个位置，然后选择作用域中的 VM 必须与之关联的保管库和备份策略。
![策略分配参数](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. 确保**效果**设置为部署"不存在"。
9. 导航到 **"审阅+创建**"，然后单击"**创建**"。

> [!NOTE]
>
> Azure 策略也可以用于现有 VM，使用[修正](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)。

> [!NOTE]
>
> 建议一次不要将此策略分配给超过 200 个 VM。 如果策略分配给 200 多个 VM，则可能导致备份比计划指定的备份晚几个小时触发。

## <a name="next-steps"></a>后续步骤

[了解有关 Azure 策略的更多](https://docs.microsoft.com/azure/governance/policy/overview)
