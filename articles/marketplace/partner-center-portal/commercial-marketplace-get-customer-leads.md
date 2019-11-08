---
title: 配置客户主管 |Azure Marketplace
description: 配置商业市场中的客户领导。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812646"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>你的 marketplace 产品/服务的客户主管

潜在顾客是对的客户感兴趣，或从[Azure Marketplace](https://azuremarketplace.microsoft.com)或[AppSource](https://appsource.microsoft.com)部署你的产品/服务。 将产品/服务发布到 marketplace 后，你将收到客户主管。 本文将介绍以下内容：

* 你的 marketplace 产品/服务如何产生客户主管，确保你不会错过商机。 
* 将 CRM 连接到你的产品/服务，以便你可以在一个中心位置管理潜在顾客。
* 了解我们向你发送的潜在客户数据，以便你可以跟进你的客户。

## <a name="generate-customer-leads"></a>生成客户主管

下面是生成潜在顾客的位置：

1. 当客户同意从 marketplace 中选择 "联系我" 后共享其信息。 这**是一项潜在的潜在客户**，我们在其中共享了有关在获取产品方面感兴趣的客户的信息。 潜在顾客位于购买漏斗的顶部。

      ![Dynamics 365 与我联系](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 当客户选择 "立即获取" 或 "创建" （在[Azure 门户](https://portal.azure.com/)中）来获取你的产品/服务时，这是一个**活动的潜在顾客**，我们会在其中共享有关已开始部署你的产品的客户的信息。

    ![SQL 立即获取](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 创建](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 客户使用 "测试驱动器" 或启动产品/服务的 "免费试用版"。 通过测试驱动器或免费试用版，你可以与潜在客户立即共享你的业务，而不会出现任何阻碍。

    ![Dynamics 365 测试驱动器](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 测试驱动器](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>连接到 CRM 系统

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>了解潜在客户数据

在客户购买过程中收到的每个潜在顾客都在特定的字段中有数据。 要查找的第一个字段是 `LeadSource` 字段，其格式如下：**源-操作** | **提供**。

**源**：此字段的值基于生成潜在顾客的 marketplace 进行填充。 可能的值为 `"AzureMarketplace"`、`"AzurePortal"`和 `"AppSource (SPZA)"`。

**操作**：此字段的值是根据客户在 marketplace 中所执行的操作（生成领导）来填充的。 

可能的值包括：

- "INS" -- 安装。 当客户购买你的产品时，将在 Azure 市场或 AppSource 中执行此操作。
- "PLT" – 表示合作伙伴引导的试用。 当客户使用“与我联系”选项时，将在 AppSource 中执行此操作。
- "DNC" - 不要联系。 请求与应用页面上交叉列出的合作伙伴联系时，将在 AppSource 中执行此操作。 我们共享以下提醒：此客户已在应用上交叉列出，但他们不需要取得联系。
- "创建"-此操作仅在 Azure 门户中，在客户向其帐户购买你的产品/服务时生成。
- "StartTestDrive" - 此操作仅适用于体验版，它是在客户启动其体验版时生成的。

**产品/服务**： marketplace 中可能有多个产品/服务。 此字段的值基于生成潜在顾客的产品/服务进行填充。 发布者 ID 和服务 ID 都发送到此字段，是你在将产品/服务发布到 marketplace 时提供的值。

下面的示例以 `publisherid.offerid`所需的格式显示示例值： 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>客户信息

通过多个字段发送客户的信息。 以下示例显示了潜在顾客中包含的客户信息。

- 名字：John
- 姓氏：Smith
- 电子邮件：jsmith\@microsoft.com
- 电话：1234567890
- 国家/地区：美国
- 公司：Microsoft
- 职称：CTO

>[!Note]
>并非以上示例中的所有数据都始终适用于每个潜在顾客。 由于你将从 "客户领导" 部分所述的多个步骤中获得线索，因此处理潜在顾客的最佳方式是取消重复记录并个性化跟进。 这样，每个客户都将收到合适的消息，并且你可以创建独特的关系。

## <a name="best-practices-for-lead-management"></a>潜在客户管理的最佳做法

1. *过程*-定义明确的销售过程，包括里程碑、kpi 和清晰的团队所有权。
2. *限定*-定义先决条件，指示是否已完全限定潜在顾客。 确保销售或市场营销代表在通过完整销售流程之前，仔细确认潜在顾客。
3. *跟进*-不要忘记跟进，预期典型事务需要5到12次调用
4. *维护*-维护你的潜在客户，让你了解更高利润的方式。

## <a name="leads-frequently-asked-questions"></a>带领常见问题

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>在哪里可以获得有关设置潜在客户目标的帮助？

可在[此处](#connect-to-your-crm-system)找到文档或通过 aka.ms/marketplacepublishersupport 提交支持票证，并选择 **"产品/服务创建"** → "**产品/服务**" **。**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>是否需要配置潜在客户目标以便在 marketplace 上发布产品/服务？

答案取决于您发布的产品/服务的类型。 适用于客户参与的 SaaS 和 Dynamics 365 产品/服务列表显示为 "联系我"，所有 Dynamics 365 for Operations 提供全部 Dynamics 365 Business Central 产品/服务，并且所有咨询服务服务都需要与潜在客户联系。 如果产品/服务类型未列出，则不是必需的。 但是，建议你配置潜在顾客目标，以便不会错过商机。

### <a name="how-can-i-find-the-test-lead"></a>如何查找测试线索？

在潜在客户目标中搜索 `"MSFT_TEST"`，以下是 Microsoft 的示例测试主管：

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>我有一个现场产品/服务，但未看到任何潜在客户？

请确保与潜在客户目标的连接有效。 在合作伙伴中心的产品/服务发布后，我们将向你发送测试主管。 如果看到测试主管，则连接有效。 你还可以在预览步骤中，通过在 marketplace 中单击 "立即获取"、"联系我" 或 "免费试用"，来尝试获取产品/服务预览版，并对其进行测试。

此外，请确保正在查找正确的数据。 本文档的 "[了解潜在客户数据](#understand-lead-data)" 一节中的内容描述了发送到潜在客户目标的潜在客户数据。

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>我已将 Azure BLOB 配置为我的潜在客户，为何我看不到潜在客户？

不再支持 Azure Blob 主导目标，因此你不会获得你的产品/服务生成的任何客户潜在顾客。 切换到任何其他[潜在客户目标选项](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>我从 Marketplace 收到了一封电子邮件，为什么我在 CRM 中找不到该潜在客户？

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会从. edu 域传递 PII 数据。 通过 aka.ms/marketplacepublishersupport 提交支持票证。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已将 Azure 表配置为我的潜在客户，如何查看潜在客户？

你可以从 Azure 门户访问存储在 Azure 表中的潜在顾客数据，也可以免费下载并安装[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)来查看 azure 存储帐户的表数据。

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>我已将 Azure 表配置为 "我的潜在客户目标"，当 Marketplace 发送新潜在客户时，是否会收到通知？

是，按照说明进行操作，设置一个 Microsoft flow，如果在[此处](./commercial-marketplace-lead-management-instructions-azure-table.md)的文档中将潜在客户添加到了 Azure 表，则发送电子邮件。

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>我已将 Salesforce 配置为我的潜在客户，为什么找不到潜在客户？

检查 "web 到潜在客户" 窗体是否为基于选择列表的必填字段。 如果是，请将字段切换为非必填文本字段。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>潜在顾客目标出现问题，我错过了某些潜在顾客。 我是否可以通过电子邮件向我发送电子邮件？

由于 PII（个人标识信息）政策，我们无法通过不安全的电子邮件共享潜在顾客信息。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已将 Azure 表配置为我的潜在客户目标，它会花费多少费用？

潜在顾客常规数据较低（几乎所有发布者都 <1 GB）。 费用将取决于收到的潜在顾客数量，如果一个月收到 1,000 个潜在顾客，则费用约为 50 美分。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

如果你的问题仍未得到解答，请通过 aka.ms/marketplacepublishersupport 联系支持人员，然后选择 **"产品/服务创建"** ，**将产品/服务的类型**→→ **"潜在客户管理配置"。** 

## <a name="next-steps"></a>后续步骤

完成技术设置后，应将这些潜在顾客合并到当前销售 & 营销策略和操作流程中。 我们希望更好地了解整个销售流程并希望更紧密地与你合作来提供高质量的潜在顾客和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果你有兴趣[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议，让你的销售团队能够更成功地利用 Marketplace 主管，请告知我们。
