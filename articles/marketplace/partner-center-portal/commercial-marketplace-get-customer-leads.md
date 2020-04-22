---
title: 配置客户潜在客户 |Azure 应用商店
description: 在商业市场中配置客户潜在客户。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731142"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>来自市场产品/服务的客户线索

潜在顾客是有兴趣的客户，或从[Azure 应用商店](https://azuremarketplace.microsoft.com)或[AppSource](https://appsource.microsoft.com)部署您的产品/服务。 产品/服务发布到市场后，您将收到客户潜在客户。 本文将解释：

* 您的市场报价如何产生客户线索，确保您不会错过商机。 
* 将 CRM 连接到您的产品/服务，以便您可以在一个中心位置管理潜在顾客。
* 了解我们向您发送的潜在客户数据，以便跟踪与您联系到的客户。

## <a name="generate-customer-leads"></a>生成客户线索

以下是生成潜在顾客的位置：

1. 当客户在从市场中选择"与我联系"后同意共享其信息时。 此潜在顾客是**初始兴趣**线索，我们在此分享有关已表示有兴趣获得您的产品的客户的信息。 潜在顾客位于购买漏斗的顶部。

      ![动态 365 联系我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 当客户选择"立即获取"或"立即获取"（在 Azure[门户](https://portal.azure.com/)中）以获取产品/服务时，此潜在顾客是**一个活动线索**，在此中，我们将共享有关已开始部署产品的客户的信息。

    ![SQL 立即获取](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![创建窗口服务器](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 客户采用"试驾"或开始"免费试用"您的优惠。 测试驱动器或免费试用是您与潜在客户即时共享业务（没有任何进入障碍）的加速机会。

    ![动态 365 试驾](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![动态 365 试驾](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>连接到 CRM 系统

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>了解潜在客户数据

在客户购买过程中收到的每个潜在顾客都在特定的字段中有数据。 要查找的第一个字段`LeadSource`是字段，该字段遵循此格式：**源操作** | **优惠**。

**源**： 此字段的值基于生成潜在顾客的市场填充。 可能的值包括 `"AzureMarketplace"`、`"AzurePortal"` 和 `"AppSource (SPZA)"`。

**操作**：此字段的值根据客户在市场中执行的操作填充，该操作生成了潜在顾客。 

可能的值包括：

- "INS" -- 安装。 当客户购买你的产品时，将在 Azure 市场或 AppSource 中执行此操作。
- "PLT" – 表示合作伙伴引导的试用。 当客户使用“与我联系”选项时，将在 AppSource 中执行此操作。
- "DNC" - 不要联系。 请求与应用页面上交叉列出的合作伙伴联系时，将在 AppSource 中执行此操作。 我们共享以下提醒：此客户已在应用上交叉列出，但他们不需要取得联系。
- "创建" - 此操作仅在 Azure 门户中，并在客户向其帐户购买产品/服务时生成。
- "StartTestDrive" - 此操作仅适用于体验版，它是在客户启动其体验版时生成的。

**优惠**： 您可能在市场上有多个优惠。 此字段的值基于生成潜在顾客的报价填充。 发布者 ID 和产品/服务 ID 都在此字段中发送，是您向市场发布产品/服务时提供的值。

以下示例以预期格式`publisherid.offerid`显示示例值： 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>客户信息

客户的信息通过多个字段发送。 下面的示例显示了潜在顾客中包含的客户信息。

- 名字：John
- 姓氏：Smith
- 电子邮件：jsmith\@microsoft.com
- 电话：1234567890
- 国家/地区：美国
- 公司：Microsoft
- 职称：CTO

>[!Note]
>并非以上示例中的所有数据都始终适用于每个潜在顾客。 由于您将从"客户潜在顾客"部分中提到的多个步骤中获得潜在顾客，因此处理潜在顾客的最佳方法是取消复制记录并个性化后续操作。 这样，每个客户都将收到合适的消息，并且你可以创建独特的关系。

## <a name="best-practices-for-lead-management"></a>潜在客户管理的最佳实践

1. *流程*- 定义明确的销售流程，具有里程碑、KPI 和明确的团队所有权。
2. *资格 -* 定义先决条件，指示潜在顾客是否已完全合格。 在将销售或营销代表接管整个销售流程之前，确保仔细鉴定潜在顾客。
3. *跟进*- 不要忘记跟进，预计典型的交易需要 5 到 12 次跟进电话
4. *培育*- 培养您的潜在客户，以便让您获得更高的利润率。

## <a name="leads-frequently-asked-questions"></a>领导常见问题

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>在何处可获得设置潜在顾客目标的帮助？

您可以[在此处](#connect-to-your-crm-system)查找文档或通过https://partner.microsoft.com/support/v2/?stage=1选择 **"产品/服务创建"** 或 **"****潜在客户管理配置"** 来查找文档或提交支持票证。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>我是否需要配置潜在顾客目标才能在市场上发布产品/服务？

答案取决于您要发布的产品/服务类型。 SaaS 和 Dynamics 365 客户参与产品优惠将列为"与我联系"、所有 Dynamics 365 运营优惠、所有 Dynamics 365 商务中心优惠以及所有咨询服务优惠都需要连接到潜在顾客目的地。 如果未列出您的要约类型，则不是必需的。 但是，建议您配置潜在顾客目标，这样您就不会错过商机。

### <a name="how-can-i-find-the-test-lead"></a>如何查找测试潜在顾客？

在潜在`"MSFT_TEST"`顾客目标中搜索，下面是来自 Microsoft 的示例测试线索：

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>我有一个现场报价，但没有看到任何线索？

确保与潜在顾客目标的连接有效。 在合作伙伴中心发布您的优惠后，我们将向您发送测试线索。 如果看到测试引线，则连接有效。 您还可以通过在预览步骤中尝试获取优惠预览来测试潜在顾客连接，单击市场上上市的"立即获取""、"联系我"或"免费试用"。

此外，请确保您正在寻找正确的数据。 本文档"[了解潜在顾客数据](#understand-lead-data)"部分的内容描述了我们发送到潜在顾客目标的风险数据。

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>我已将 Azure BLOB 配置为我的潜在顾客目标，为什么看不到潜在顾客？

Azure Blob 潜在顾客目标不再受支持，因此您缺少产品/服务生成的任何客户潜在顾客。 切换到任何其他[潜在顾客目标选项](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>我收到了来自市场的电子邮件，为什么找不到 CRM 中的潜在顾客？

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会从 .edu 域传递私人身份信息。 通过https://partner.microsoft.com/support/v2/?stage=1提交支持票证。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已经将 Azure 表配置为我的潜在顾客目标，如何查看潜在顾客？

可以从 Azure 门户访问存储在 Azure 表中的潜在数据，也可以免费下载和安装[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 存储帐户的表数据。

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>已将 Azure 表配置为潜在顾客目标，可在市场发送新潜在顾客时收到通知吗？

可以，按照说明设置 Microsoft 流，如果[此处](./commercial-marketplace-lead-management-instructions-azure-table.md)的文档将潜在顾客添加到 Azure 表，则该流将发送电子邮件。

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>已将 Salesforce 配置为潜在顾客目标，为什么找不到潜在顾客？

检查"Web 到潜在顾客"窗体是否是基于选取列表的必填字段。 如果是，请将字段切换为非必填文本字段。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>潜在顾客目标存在问题，我丢失了一些潜在顾客。 可通过电子邮件将他们发送给我吗？

由于私人可识别信息政策，我们无法通过不安全的电子邮件共享潜在顾客信息。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已经将 Azure 表配置为我的潜在顾客目标，它将花费多少？

潜在顾客常规数据较低（几乎所有发布者都 <1 GB）。 费用将取决于收到的潜在顾客数量，如果一个月收到 1,000 个潜在顾客，则费用约为 50 美分。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

如果您的问题仍未得到解答，请通过aka.ms/marketplacepublishersupport联系支持人员，然后选择 **"产品/服务创建"+****您的产品/服务类型**和 **"潜在客户管理配置"。** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>收到新客户线索时，我收到电子邮件通知。 如何配置接收这些电子邮件的人员？

在合作伙伴中心访问您的优惠，并导航到**产品/服务设置**页面 ->**潜在顾客管理** -> **编辑**。 更新 **"联系人电子邮件**"字段下的电子邮件地址。

## <a name="next-steps"></a>后续步骤

一旦技术设置到位，您应该将这些潜在顾客纳入您当前的销售&营销战略和运营流程。 我们希望更好地了解整个销售流程并希望更紧密地与你合作来提供高质量的潜在顾客和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果您有兴趣[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议，使您的销售团队在市场线索方面更加成功，请告诉我们。
