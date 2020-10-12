---
title: 在 Azure 开发测试实验室中向实验室添加标记 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中创建自定义标记并使用标记对资源进行分类。 你可以查看订阅中具有标记的所有资源。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270727"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中向实验室添加标记

可以创建自定义标记，并将它们应用于开发测试实验室资源，以便能够按逻辑对资源进行分类。 后来，可以快速轻松地在订阅中找到全部具有相应标记的资源。 如果为了方便计费或管理而需要整理资源，就会发现标记十分有用。

支持应用标记的资源包括

* 计算 VM
* NIC
* IP 地址
* 负载均衡器
* 存储帐户
* 托管磁盘

可以在[创建实验室](devtest-lab-create-lab.md)时应用标记，稍后通过“配置和设置”下的“标记”边栏选项卡管理标记。

每个标记都由一个**名称**/ / **值**对组成。 例如，可以创建一个标记，其中名称 costcenter** 对应的值为 34543**。 此类标记可能有助于稍后发现在组织的这一特定区域可计费的实验室资源。 可以选择对所需订阅整理方式有意义的名称和值。

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>在现有实验室中管理标记的具体步骤

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 如果需要，请选择“所有服务”****，并从列表中选择“开发测试实验室”****。 你的实验室可能已显示在 " **所有资源**" 下的仪表板中。
1. 在实验室列表中，选择要在其中添加或管理标记的实验室。
1. 在实验室的“概览”**** 区域中，选择“配置和策略”****。

    ![“配置和策略”按钮](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. 在左侧的“管理”**** 下，选择“标记”****。
1. 若要为此实验室创建新标记，请输入 "**名称**" / **值**对，然后选择 "**保存**"。 还可以从列表中选择现有标记，从而查看或管理与此标记相关联的资源。

    ![管理标记](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> 在实验室级别创建的标记会流过实验室在你的订阅中的所有可计费资源。 例如，实验室级标记流向实验室 Vm 的基础计算 Vm。您可以在成本管理的上下文中使用标记。 实验室级标记显示在用于成本管理的标记筛选器中。

## <a name="understanding-limitations-to-tags"></a>了解标记限制

以下限制适用于标记：

* 每个资源或资源组最多可以有 15 个标记名称值对。 此限制仅适用于直接应用到资源组或资源的标记。 资源组可以包含多个资源，这些资源每个都有 15 个标记名称值对。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 应用于资源组的标记不会被该资源组中的资源继承。

[使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)更详细地介绍了如何在 Azure 中使用标记，包括如何使用 PowerShell 或 Azure CLI 管理标记。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
* 可使用自定义策略对订阅应用限制和约定。 定义的策略可能要求所有资源具有特定标记的值。 有关详细信息，请参阅[设置策略和计划](devtest-lab-set-lab-policy.md)。
* 探索 [开发测试 Labs Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。
