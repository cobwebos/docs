---
title: 更新现有的 Azure SaaS 应用程序产品/服务 | Microsoft Docs
description: 如何更新 Azure 市场上的现有 SaaS 应用程序产品/服务。
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594283"
---
# <a name="update-an-existing-saas-application-offer"></a>更新现有的 SaaS 应用程序产品/服务

产品/服务发布并推出后，你可能需要执行各种类型的更新。 你对新版产品/服务进行的任何更改，均应保存并重新发布，以使其反映在市场中。 本文分步介绍了在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新 SaaS 产品/服务的各个方面。

我们可能会出于多个原因更新套餐，例如：

- 向现有应用添加新版本。
- 更新应用。
- 向应用添加新功能。
- 更新产品/服务的市场元数据。

为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。

## <a name="unpermitted-changes-to-a-saas-offer"></a>SaaS 产品/服务的未经许可更改

在 Azure 市场中推出产品/服务后，无法更改 SaaS 产品/服务的某些属性。 不能更改以下设置：

- 产品/服务的“产品/服务 ID”和“发布者 ID”
- 版本标记，例如 `1.0.1`
- 对现有产品/服务的计费/许可证模型更改。

## <a name="common-update-operations"></a>常见更新操作
 
以下更新操作很常见。

### <a name="update-offer-contacts"></a>更新产品/服务联系人

使用以下步骤更新产品/服务的支持联系人。

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有产品/服务”下，找到要更新的产品/服务。
3. 转到“联系人”选项卡。更新你的联系人。
4. 选择“发布”，启动发布更改的工作流。


### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据。 （例如：公司名称、徽标等）

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有产品/服务”下，找到要更新的产品/服务。
3. 转到“店面详细信息”选项卡。使用[发布 SaaS 产品/服务](./cpp-publish-offer.md)中的说明进行元数据更改。
4. 选择“发布”，启动发布更改的工作流。

## <a name="compare-feature"></a>“比较”功能

对已发布的产品/服务进行更改时，可以使用“比较”功能来审核所做的更改。 下面的屏幕截图显示了已发布的产品/服务的“比较”选项。

![在云合作伙伴门户中使用“比较”查看产品/服务的更改](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>若要使用“比较”功能，请执行以下操作：

1. 在编辑过程中的任意时间点，针对你的产品/服务选择“比较”。
2. 并列查看营销资产和元数据的版本。

## <a name="publishing-history"></a>发布历史记录

若要查看历史发布活动，请选择云合作伙伴门户左侧导航菜单栏中的“历史记录”选项卡。 可以看到在 Azure 市场套餐生存期内执行的带时间戳的操作。

![在云合作伙伴门户中查看产品/服务历史记录](./media/saas-offer-history.png)

可以使用“审核历史记录”页面搜索特定的产品/服务并应用筛选器，例如“发布者”、“产品/服务”和“事件类型”（例如 OfferGoLiveRequested）。还可以将审核历史记录下载为 csv 文件。


## <a name="next-steps"></a>后续步骤

[SaaS 应用程序产品/服务](./cpp-saas-offer.md)