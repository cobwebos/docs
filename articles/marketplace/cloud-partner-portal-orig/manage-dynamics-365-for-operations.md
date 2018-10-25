---
title: 如何通过云合作伙伴门户创建 Dynamics 365 for Operations 套餐 | Microsoft Docs
description: 如何通过云合作伙伴门户创建 Dynamics 365 for Operations 套餐
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: f2e0c7b335b09a0d2b8b8399e807a354ec8e4282
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805575"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>如何通过云合作伙伴门户创建 Dynamics 365 for Operations 套餐

发布门户提供对门户的基于角色的访问，使多个人员能够在发布套餐方面展开协作。 有关详细信息，请参阅[在云门户中管理用户](./cloud-partner-portal-manage-users.md)。

代表发布者帐户发布套餐之前，其中一个具有\"所有者\"角色的个人需同意遵守[使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、 [Microsoft 隐私声明](http://www.microsoft.com/privacystatement/default.aspx)以及 [Microsoft Azure 认证计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>如何创建新的 Dynamics 365 for Operations 套餐

满足所有先决条件后，可以开始创作 Dynamics 365 for Operations 套餐。

1. 登录[云合作伙伴平台](http://cloudpartner.azure.com/)。
2. 在左侧导航栏中单击“+ 新建套餐”，然后选择“Dynamics 365 for Operations”。\"\"\"\"
3. 新套餐的“编辑器”视图现在可供使用，我们随时可以开始创作。\"\"
4. 需要填写的窗体显示在“编辑器”视图的左侧。\"\"\"\" 每个\"窗体\"都包含一组要填写的字段。必填字段标有红色的星号 (\*)。

有四个主窗体用于创作 Dynamics 365 for Operations 套餐：

- 产品/服务设置
- 技术信息
- 店面详细信息
- 联系人

## <a name="how-to-fill-out-the-offer-settings-form"></a>如何填写产品/服务设置表单

产品/服务设置表单是用于指定产品/服务设置的基本表单。 不同的字段如下所述。

### <a name="offer-id"></a>套餐 ID

这是发布者个人资料内产品/服务的唯一标识符。 此 ID 将显示在产品 URL 中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。

例如，如果发布者 contoso 创建了 ID 为 *sample-dynamics365 for operations* 的套餐，该套餐将以 \"https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics 365 for operations*?tab=Overview\" 的形式显示在 AppSource 中

### <a name="publisher-id"></a>发布者 ID

通过此下拉列表，可以选择要在其下发布此产品/服务的发布者个人资料。 此字段在套餐推出后处于锁定状态。

名称

这是产品/服务的显示名称。 这是显示在 [AppSource](https://appsource.microsoft.com) 中的名称。 该名称不能超过 50 个字符。

单击“保存”以保存进度。\"\" 下一步是填写套餐的技术信息。

## <a name="how-to-fill-out-the-technical-info-form"></a>如何填写“技术信息”窗体

技术信息窗体包含要显示在套餐页中的信息。 下面描述了不同字段的说明。

![“新建套餐”屏幕](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>解决方案标识符

首先是解决方案标识符。

1. 若要查找此标识符，请转到“生命周期服务”并选择“解决方案管理”。
2. 选取合适的解决方案后，在“包概述”中可以看到“解决方案标识符”。 \*\*如果标识符为空，请选择“编辑”并重新发布包，如此即可显示解决方案标识符。

### <a name="validation-assets"></a>验证资产

此处上传 CAR（自定义分析报告）。

### <a name="does-solution-enable-translations"></a>解决方案是否支持翻译?

选择“是”或“否”。\'\'\'\'

### <a name="does-solution-include-localizations"></a>解决方案是否包含本地化内容?

选择“是”或“否”。\'\'\'\'

### <a name="product-version"></a>产品版本

选择“新建 AX”。 最后单击“保存”。

## <a name="how-to-fill-out-storefront-details-form"></a>如何填写店面详细信息窗体。

首先是“套餐详细信息”。

1. **套餐摘要**

    \- 输入解决方案的简短摘要（最多 100 个字符）。

2. **套餐说明**

    \- 输入解决方案的简要说明。 说明中应该包含解决方案的功能范围，应与 BPM 库直接相符。 例如，假设营销内容包含功能 x、y、z，则在最终评审过程中，我们将确保 LCS 中的 BPM 库阐述了这些功能。

![店面详细信息屏幕](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>商品详细信息

![店面详细信息屏幕](./media/publish_d365_new_offer/storefront_details.png)

1. **行业** - 从给定的选项中最多选中两个行业。
2. **类别** - 从给定的选项中最多选中三个类别。
3. **应用类型** - 从给定的选项中选择。
4. **应用的帮助链接** - 输入解决方案的帮助链接。
5. **支持的国家/地区** - 从给定的选项中选择。
6. **支持的语言** - 从给定的选项中选择。
7. **应用版本** - 输入所要发布的解决方案的版本。 （例如 1.0.0.0）
8. **应用发布日期** - 输入解决方案的发布日期 (mm/dd/yyyy)。
9. **可与应用配合使用的产品** - 可与应用配合使用的产品（仅限列表中的选项）。 最多可以列出三个产品。 若要列出产品，请单击“新建”旁边的加号，此时会创建一个新的文本字段，请在其中输入可与应用配合使用的产品名称。
10. **搜索关键字** - 输入用户在搜索期间可能用来查找该解决方案的常用字词。 \*\*这些关键字不会显示在市场中。
11. **隐藏密钥** - 这是与套餐预览 URL 结合使用的、以便在公共视图中隐藏该套餐的密钥。 这并非密码。 可在此处输入任意字符串。

### <a name="marketing-artifacts"></a>营销项目

1. 下一步是上传**徽标**和**屏幕截图**。 \*\*请注意每个上传内容的大小，所有图像应采用 PNG 格式。
2. **演示视频** - 单击“+ 新建”。\"\" 上传解决方案的演示视频（仅限 YouTube 或 Vimeo 链接）。\*\* 请注意，应上传指定大小的缩略图，以便临时显示视频。
3. **文档** - 上传与解决方案相关的任何文档，并记得输入文档的名称。

### <a name="legal"></a>合法

此信息将链接到隐私政策和使用条款。 输入解决方案的隐私政策 URL 和使用条款。 \*\*客户可以在门户中看到这些政策。

### <a name="customer-support"></a>客户支持

用户只会在门户中看到支持 URL。

### <a name="leads-management"></a>潜在顾客管理

选择用于存储潜在顾客的 CRM 系统。 如果有以下 CRM 系统之一，请在此处选择\"Azure 表\"：Salesforce、Marketo、Microsoft Dynamics CRM。 在 AppSource 中试用应用的最终用户（潜在顾客）的详细信息将写入到此处所选的 CRM 系统。 根据选择的 CRM 系统，单击以下相应 URL，以获取有关如何完成下一组字段的信息。

![潜在顾客管理详细信息](./media/publish_d365_new_offer/leads.png)

1. 对于 Azure 表，请参阅[此文](https://aka.ms/leadsettingforazuretable)
2. 对于 Dynamics CRM Online，请参阅[此文](https://aka.ms/leadsettingfordynamicscrm)
3. 对于 Marketo，请参阅[此文](https://aka.ms/leadsettingformarketo)
4. 对于 Salesforce，请参阅[此文](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>如何填写“联系人”窗体。

此信息用于 Microsoft 和客户支持。 输入公司的工程联系人和客户支持人员，以及解决方案的支持 URL。 如果 Microsoft 对你的解决方案存在疑问，此信息将用作单一联系点，另外，它还用于客户支持。

![套餐联系人屏幕](./media/publish_d365_new_offer/Contacts.png)
