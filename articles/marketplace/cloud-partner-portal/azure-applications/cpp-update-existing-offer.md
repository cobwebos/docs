---
title: 更新现有的 Azure 应用程序产品/服务 | Microsoft Docs
description: 如何更新 Azure 市场中的现有 Azure 应用程序产品/服务。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744612"
---
# <a name="update-an-existing-azure-application-offer"></a>更新现有的 Azure 应用程序产品/服务

产品/服务发布并上线后，你可能需要执行各种类型的更新。 你对新版产品/服务进行的任何更改，均应保存并重新发布，以使其反映在市场中。 本文分步介绍了在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新托管应用程序产品/服务的各个方面。

我们可能会出于多个原因更新套餐，例如：

- 将新的映像版本添加到现有 SKU 中。
- 添加新的 SKU。
- 更新套餐或单个 SKU 的市场元数据。

为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Azure 应用程序产品/服务或 SKU 的未经许可更改

在 Azure 市场中推出产品/服务后，无法更改容器产品/服务或 SKU 的某些属性。 不能更改以下设置：

- 产品 ID 和产品发布者 ID
- 现有 SKU 的 SKU ID
- 版本标记，例如 `1.0.1`
- 对现有 SKU 的计费/许可证模型更改

## <a name="common-update-operations"></a>常见更新操作

以下更新操作很常见。

### <a name="update-image-version-for-a-sku"></a>更新 SKU 的映像版本

我们经常会使用安全修补程序、其他功能等定期更新映像。 在这种情况下，需要使用以下步骤来更新 SKU 引用的映像：

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有套餐”下，找到要更新的套餐。
3. 在“SKU”选项卡中，选择与要更新的映像关联的 SKU。
4. 选择“+ 新建映像版本”以添加新的映像。
5. 提供新的映像版本。 映像版本需要遵循与以前版本相同的标记准则。 版本标记应采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。 检查提供的新版本是否高于以前的所有版本。
6. 选择“发布”以启动将新映像版本发布到 Azure 市场的工作流。

### <a name="add-a-new-sku"></a>添加新 SKU

使用以下步骤使新 SKU 可用于套餐：

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有套餐”下，找到要更新的套餐。
3. 在“SKU”选项卡下，选择“添加新 SKU”并在弹出窗口中提供一个 **SKU ID**。
4. 使用[发布 Azure 应用程序产品/服务](./cpp-publish-offer.md)中描述的步骤重新发布产品/服务。
5. 选择“发布”，启动发布新 SKU 的工作流。

### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据。 （例如：公司名称、徽标等）

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有产品/服务”下，找到要更新的产品/服务。
3. 转到“市场”选项卡。使用[发布 Azure 应用程序产品/服务](./cpp-publish-offer.md)中的说明进行元数据更改。
4. 选择“发布”，启动发布更改的工作流。
 
>[!Note]
>云解决方案提供商 (CSP) 合作伙伴通道参加现已推出。  请参阅[云解决方案提供商](../../cloud-solution-providers.md)营销通过 Microsoft CSP 产品/服务的详细信息的合作伙伴渠道。

## <a name="deleting-an-existing-offer"></a>删除现有产品/服务

你可能决定从市场中删除产品/服务。 删除产品/服务后不会影响当前已购买此产品/服务的客户。 这些客户可以像以前一样继续使用。 但是，删除完成后，该产品/服务将不再能够产生任何新的购买活动。

产品/服务终止是终止该服务和/或你与现有客户之间许可协议的过程。
有关产品/服务删除和终止的指南和相关策略均受“Microsoft 市场发布者协议”（请参阅第 7 节）和“参与策略”（请参阅 6.2 节）约束。

有关详细信息，请参阅[从 Azure 市场中删除产品/服务/SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete)。

## <a name="compare-feature"></a>“比较”功能

对已发布的产品/服务进行更改时，可以使用“比较”功能来审核所做的更改。

若要使用“比较”功能，请执行以下操作：

1. 在编辑过程中的任意时间点，针对你的产品/服务选择“比较”。
2. 并列查看营销资产和元数据的版本。

## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看历史发布活动，请选择云合作伙伴门户左侧导航菜单栏中的“历史记录”选项卡。 可以看到在 Azure 市场套餐生存期内执行的带时间戳的操作。

## <a name="next-steps"></a>后续步骤

[Azure 应用程序产品/服务](./cpp-azure-app-offer.md)