---
title: 教程 - 使用 Azure 门户新建策略分配
description: 本教程介绍如何使用 Azure 门户创建 Azure Policy 分配以识别不合规的资源。
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826594"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>教程：创建策略分配以识别不合规资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 Azure Policy 支持通过来宾配置策略来审核启用了 Arc 的服务器的状态。 来宾配置策略不会应用配置，它们仅审核计算机内部的设置。 本教程将指导你完成创建和分配策略的过程，并确定启用了 Arc 的服务器中哪些服务器未安装 Log Analytics 代理。

在此过程结束时，你将成功识别未安装 Windows 或 Linux 版 Log Analytics 代理的计算机。 这些虚拟机不符合策略分配要求。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="create-a-policy-assignment"></a>创建策略分配

在本教程中，将创建一个策略分配，并分配 _\[预览版]：_ Log Analytics 代理应安装在 Linux Azure Arc 计算机中”策略定义。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。 

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

1. 选择“Azure Policy”页左侧的“分配”  。 分配即为在特定范围内分配策略以供执行。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

1. 在“策略 - 分配”页的顶部选择“分配策略”   。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

1. 在“分配策略”页上，通过单击省略号并选择管理组或订阅，选择“范围”********。 或者，请选择一个资源组。 范围用于确定对其强制执行策略分配的资源或资源组。 然后在“范围”页的底部单击“选择”。********

   此示例使用“Parnell Aerospace”订阅。 你的订阅将有所不同。

1. 可基于“范围”排除资源****。 “排除”从低于“范围”级别的一个级别开始********。 “排除”是可选的，因此暂时将其留空****。

1. 选择“策略定义”旁边的省略号打开可用定义的列表。**** Azure Policy 附带可供使用的内置策略定义。 许多是可用的，例如：

   - 强制实施标记和值
   - 应用标记和值
   - 从资源组继承标记（如果缺少此标记）

   有关可用内置策略的部分列表，请参阅 [Azure Policy 示例](../../../governance/policy/samples/index.md)。

1. 如果在基于 Windows 的计算机上已启用启用了 Arc 的服务器（预览版）代理，则搜索策略定义列表并找到“\[预览]：Log Analytics 代理应安装在 Windows Azure Arc 计算机中”定义。 对于基于 Linux 的计算机，请找到相应的“\[预览]：Log Analytics 代理应安装在 Linux Azure Arc 计算机中”策略定义。 单击该策略，再单击“选择”****。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

1. “分配名称”中自动填充了所选的策略名称，但可以更改它。 对于此示例，请根据自己的选择，保留“\[预览]：Log Analytics 代理应安装在 Windows Azure Arc 计算机中”或“\[预览]：Log Analytics 代理应安装在 Linux Azure Arc 计算机中”。 还可根据需要添加“说明”。 该说明提供有关此策略分配的详细信息。
   将根据登录的用户自动填写“分配者”****。 此字段是可选字段，因此可输入自定义值。

1. 不选中“创建托管标识”****。 当策略或计划包含具有 [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) 效果的策略时，必须选中此复选框__。 由于本快速入门所使用的策略中未包含上述策略，请将其留空。 有关详细信息，请参阅[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)和[修正安全性工作原理](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)。

1. 单击“分配”。

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

选择页面左侧的“符合性”。 然后找到创建的“\[预览]：Log Analytics 代理应安装在 Windows Azure Arc 计算机中”或“\[预览]：Log Analytics 代理应安装在 Linux Azure Arc 计算机中”策略分配。

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

如果存在与此新分配不相符的任何现有资源，这些资源会在“不符合的资源”下显示  。

针对现有资源评估某条件时，如果结果为 true，则会将这些资源标记为与策略不符。 下表显示了对于生成的符合性状态，不同的策略效果是如何与条件评估配合使用的。 尽管在 Azure 门户中看不到评估逻辑，但会显示符合性状态结果。 符合性状态结果为符合或不符合。

| **资源状态** | **效果** | **策略评估** | **符合性状态** |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不合规 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 符合 |
| 新建 | Audit、AuditIfNotExist\* | True | 不合规 |
| 新建 | Audit、AuditIfNotExist\* | False | 符合 |

\*Append、DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 语句为 TRUE。
这些效果还要求存在条件为 FALSE 才能将资源判定为不合规。 如果为 TRUE，则 IF 条件会触发相关资源存在条件的计算。

## <a name="clean-up-resources"></a>清理资源

删除创建的分配，请执行以下步骤：

1. 选择 Azure Policy 页面左侧的“合规性”（或“分配”）并找到创建的“\[预览]：  Log Analytics 代理应安装在 Windows Azure Arc 计算机中”或“\[预览]：Log Analytics 代理应安装在 Linux Azure Arc 计算机中”策略分配。

1. 右键单击策略分配，然后选择“删除分配”。

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

## <a name="next-steps"></a>后续步骤

在本教程中，你向某个范围分配了策略定义并评估了其合规性报告。 策略定义可验证范围内的所有资源都符合策略，并可标识不符合策略的资源。 现在，你已准备好通过用于 VM 的 Azure Monitor 监视启用了 Azure Arc 的服务器计算机。

若要了解如何监视和查看计算机的性能、正在运行的进程及其依赖项，请继续学习教程：

> [!div class="nextstepaction"]
> [启用用于 VM 的 Azure Monitor](tutorial-enable-vm-insights.md)