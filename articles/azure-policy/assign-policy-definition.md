---
title: "创建策略分配以识别 Azure 环境中的不合规资源 | Microsoft Docs"
description: "本文引导你完成创建策略定义的步骤，以识别不合规的资源。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/10/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 4287b139f26d17e58f6caffbadb2c7da2a9b7b82
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>创建策略分配，识别 Azure 环境中的不合规资源
若要了解 Azure 中的符合性，第一步是确定资源的状态。 本快速入门教程指导你完成创建策略分配的过程，以识别未使用托管磁盘的虚拟机。

此过程结束时，就会成功识别哪些虚拟机未使用托管磁盘。 这些虚拟机*不符合*策略分配要求。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-policy-assignment"></a>创建策略分配

在本快速入门中，请创建一个策略分配，分配“审核缺少托管磁盘的虚拟机”策略定义。

1. 选择 Azure 策略页左侧窗格中的“分配”。
2. 选择“分配”窗格顶部的“分配策略”。

   ![分配策略定义](media/assign-policy-definition/select-assign-policy.png)

3. 在“分配策略”页上，单击“策略”字段旁边的![策略定义按钮](media/assign-policy-definition/definitions-button.png)，打开可用定义的列表。

   ![打开可用策略定义](media/assign-policy-definition/open-policy-definitions.png)

   Azure 策略附带可供使用的内置策略定义。 内置策略定义如下：

   - 强制实施标记和值
   - 应用标记和值
   - 需要 SQL Server 版本 12.0

    如需所有可用内置策略的完整列表，请参阅[策略模板](json-samples.md)。

4. 搜索策略定义，找到“审核不使用托管磁盘的 VM”定义。 单击该策略，再单击“选择”。

   ![找到正确的策略定义](media/assign-policy-definition/select-available-definition.png)

5. 提供策略分配的显示名称。 在此示例中，我们使用“审核缺少托管磁盘的 VM”。 还可根据需要添加“说明”。 该说明详细介绍了此策略分配如何标识所有不使用托管磁盘的虚拟机。
6. 将定价层更改为“标准”，确保策略应用于现有资源。

   Azure 策略内有两个定价层 - 免费和标准。 使用免费层，只能对将来资源强制实施策略；使用标准层，还可对现有资源强制实施策略，更好地了解符合性状态。 若要详细了解定价，请参阅 [Azure 策略定价](https://azure.microsoft.com/pricing/details/azure-policy/)。

7. 选择要向其应用策略的范围。  范围用于确定对其强制执行策略分配的资源或资源组。 它可以从订阅延伸至资源组。
8. 选择之前注册的订阅（或资源组）。 此示例使用“Azure 分析容量开发”订阅，你的选项可能有所不同。 单击“选择”。

   ![找到正确的策略定义](media/assign-policy-definition/assign-policy.png)

9. 目前请将“排除项”留空，然后单击“分配”。

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

选择左侧窗格中的“符合性”，搜索创建的策略分配。

![策略符合性](media/assign-policy-definition/policy-compliance.png)

如果存在与此新分配不相符的任何现有资源，这些资源会在“不符合的资源”下显示。

针对现有资源评估某条件时，如果结果为 true，则会将这些资源标记为与策略不符。 上一示例图显示了不符合的资源。 下表显示对于生成的符合性状态，不同的策略操作是如何与条件评估配合使用的。 虽然不显示 Azure 门户中的评估逻辑，但会显示符合性状态结果。 符合性状态结果为符合或不符合。

|资源  |如果策略中条件的评估结果为  |策略中的操作   |符合性状态  |
|-----------|---------|---------|---------|
|Exists     |True     |拒绝     |不符合 |
|Exists     |False    |拒绝     |符合     |
|Exists     |True     |附加   |不符合 |
|Exists     |False    |附加   |符合     |
|Exists     |True     |审核    |不符合 |
|Exists     |False    |审核    |不符合 |

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他指南建立在本快速入门的基础之上。 如何打算继续浏览后续教程，请勿清除本快速入门中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此快速入门创建的所有资源。
1. 选择左侧窗格中的“分配”。
2. 搜索创建的分配，然后右键单击它。

   ![删除分配](media/assign-policy-definition/delete-assignment.png)

3.  选择“删除分配”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何向范围分配策略定义。 策略定义可确保范围内的所有资源都符合策略，并可标识不符合策略的资源。

若要详细了解分配策略以确保将来所创建资源的符合性，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./create-manage-policy.md)
