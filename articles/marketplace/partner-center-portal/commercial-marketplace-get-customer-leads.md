---
title: Microsoft 商业应用商店中的潜在客户管理
description: 了解如何通过 Microsoft AppSource 和 Azure Marketplace 产品/服务生成和接收客户主管
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5d1bf859968c7b7a889abe635c917d0da11bf6e1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837322"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>客户从你的商业应用商店产品/服务

潜在顾客是对[Microsoft AppSource](https://appsource.microsoft.com)和[Azure Marketplace](https://azuremarketplace.microsoft.com)中的产品/服务感兴趣的客户。 你可以在产品/服务发布到商业市场后接收客户领导。 本文介绍了以下潜在的管理概念：

* 你的商业应用商店产品/服务如何产生客户主管，确保你不会错过商机。 
* 如何将您的客户关系管理（CRM）系统连接到您的产品/服务，以便您可以在一个中心位置管理潜在顾客。
* 发送给你的潜在客户数据可以跟进你的客户。

## <a name="generate-customer-leads"></a>生成客户主管

下面是生成潜在顾客的位置：

- 客户同意在他们选择了 "从商业市场**联系我**" 后共享其信息。 这*是一个潜在的潜在顾客*。 我们会与你共享信息，以了解在获取你的产品时有兴趣的客户。 潜在顾客位于购买漏斗的顶部。

    ![Dynamics 365 与我联系](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 客户选择**立即获取**（或在[Azure 门户](https://portal.azure.com/)中选择 "**创建**"）来获取你的产品/服务。 这是一个*活动*潜在顾客。 我们会与你分享有关已开始部署你的产品的客户的信息。

    ![SQL 立即获取按钮](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server "创建" 按钮](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 客户选择 "**测试驱动器**" 或 "**免费试用版**" 来试用产品/服务。 通过测试驱动器或免费试用版，你可以与潜在客户立即共享你的业务，而不会出现任何阻碍。

    ![Dynamics 365 "测试驱动器" 按钮](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 "免费试用" 按钮](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>连接到 CRM 系统

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>了解潜在客户数据

在客户购买过程中收到的每个潜在顾客都在特定的字段中有数据。 要查找的第一个`LeadSource`字段是字段，其格式如下：**源-操作** | **提供**。

**源**：此字段的值基于生成潜在顾客的 marketplace 进行填充。 可能的值包括 `"AzureMarketplace"`、`"AzurePortal"` 和 `"AppSource (SPZA)"`。

**操作**：此字段的值是根据客户在生成领导的 marketplace 中所采取的操作进行填充的。

可能的值为：

- **"INS"**：代表*安装*。 当客户获取你的产品时，此操作位于 Azure Marketplace 或 AppSource 中。
- **"PLT"**：代表*合作伙伴 led 的试用版*。 当客户选择 "**与我联系**" 选项时，此操作在 AppSource 中。
- **"DNC"**：代表 "不*联系*"。 当请求联系在应用页面上交叉列出的合作伙伴时，此操作位于 AppSource 中。 我们共享了此客户在你的应用上交叉列出的通知，但不需要联系这些通知。
- **"创建"**：此操作仅在 Azure 门户中，当客户将你的产品/服务购买到帐户时，将生成此操作。
- **"StartTestDrive"**：此操作仅适用于 "**测试驱动器**" 选项，在客户启动其测试驱动器时生成。

**产品/服务**：你的商业应用商店中可能有多个产品/服务。 此字段的值基于生成潜在顾客的产品/服务进行填充。 发布者 ID 和服务 ID 都发送到此字段，是你在将产品/服务发布到 marketplace 时提供的值。

下面的示例以预期格式`publisherid.offerid`显示值： 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>客户信息

通过多个字段发送客户的信息。 以下示例显示了潜在顾客中包含的客户信息：

- 名字：John
- 姓氏：Smith
- 电子邮件：jsmith\@microsoft.com
- 电话：1234567890
- 国家/地区：美国
- 公司：Microsoft
- 职称：CTO

>[!NOTE]
>并非以上示例中的所有数据都始终适用于每个潜在顾客。 由于你将从 "生成客户主管" 部分中提到的多个步骤中获得线索，因此处理潜在顾客的最佳方式是取消复制记录并个性化跟进。 这样，每个客户都可以获得相应的消息，并创建唯一关系。

## <a name="best-practices-for-lead-management"></a>潜在客户管理的最佳做法

- **过程**：定义明确的销售过程，其中包含里程碑、分析和清晰的团队所有权。
- **资格**：定义必备项，用于指示潜在客户是否完全限定。 请确保销售或营销代表在通过完整销售流程之前，请仔细确认潜在顾客。
- **跟进**：不要忘记跟进。 预计典型事务需要5到12次调用。
- **维护**：维护你的潜在客户，让你了解更高利润的方式。

## <a name="common-questions-about-lead-management"></a>有关潜在客户管理的常见问题

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>在何处可获得设置潜在顾客目标的帮助？

按照[连接到 CRM 系统](#connect-to-your-crm-system)部分中的步骤进行操作，或通过[合作伙伴中心帮助和支持](https://partner.microsoft.com/support/v2/?stage=1)提交支持票证。 然后选择 "**产品/服务** > " "产品 > **/服务"**"产品 **/服务"**。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>是否需要配置潜在客户目标以便在商业应用商店中发布产品/服务？

答案取决于要发布的产品/服务的类型。 "软件即服务（SaaS）" 和 "Dynamics 365 客户参与" 使用 "**联系我**"，列出所有 dynamics 365 for 金融和运营产品/服务、所有 Dynamics 365 Business Central 产品和所有咨询服务产品/服务。 因此，它们需要与潜在客户目标建立连接。 如果你的产品/服务类型未列出，则不需要与潜在客户目标建立连接。 建议你配置潜在顾客目标，以便不会错过商机。

### <a name="how-can-i-find-the-test-lead"></a>如何查找测试潜在顾客？

`"MSFT_TEST"`在潜在客户目标中搜索。 下面是 Microsoft 的示例测试主管：

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有一个现场促销活动，但为什么我不会看到任何潜在客户？

请确保与潜在客户目标的连接有效。 在合作伙伴中心选择 "**发布**" 后，我们将向你发送测试主管。 如果看到测试主管，则连接有效。 你还可以通过在预览步骤中尝试获取产品/服务预览版来测试你的潜在客户连接。 选择 "**立即获取**"、"**与我联系**" 或 "**免费试用**"。

此外，请确保正在查找正确的数据。 本文的 "[了解潜在客户数据](#understand-lead-data)" 一节中的内容介绍了发送到潜在客户目标的潜在客户数据。

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已将 Azure Blob 存储配置为我的潜在客户，但是为什么看不到潜在客户？

Azure Blob 存储不再作为潜在客户目标提供支持，因此你不会遇到你的产品/服务生成的任何客户顾客。 切换到任何其他[潜在客户目标选项](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到了来自商业应用商店的一封电子邮件，但为何我在 CRM 中找不到潜在客户？

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会将 edu 域中的个人信息传递给你。 通过[合作伙伴中心帮助和支持](https://partner.microsoft.com/support/v2/?stage=1)提交支持票证。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我将 Azure 表配置为 "我的潜在客户"。 如何查看潜在客户？

可以从 Azure 门户访问存储在 Azure 表中的潜在顾客数据。 你还可以免费下载和安装[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，以便查看 Azure 存储帐户的表数据。

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我将 Azure 表配置为 "我的潜在客户"。 当发送新的商业市场主管时，是否可以收到通知？

可以。 按照[使用 azure 表配置潜在客户管理](./commercial-marketplace-lead-management-instructions-azure-table.md)中的说明进行操作，以设置 Microsoft flow，以便在将潜在客户添加到 Azure 表时发送电子邮件。

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我将 Salesforce 配置为我的潜在客户，但为什么找不到潜在客户？

根据选取列表检查 web 到潜在客户窗体是否为必填字段。 如果是，请将该字段切换为非强制性文本字段。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>潜在顾客目标存在问题，我丢失了一些潜在顾客。 可通过电子邮件将他们发送给我吗？

由于个人信息策略，我们无法通过不安全的电子邮件共享潜在客户信息。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我将 Azure 表配置为 "我的潜在客户"。 它的费用是多少？

潜在客户生成数据较低。 几乎所有发布服务器都小于 1 GB。 成本取决于接收到的潜在客户数。 例如，如果每个月收到1000个潜在顾客，则成本大约为50美分。 有关存储定价的详细信息，请参阅[Azure 存储概述定价](https://azure.microsoft.com/pricing/details/storage/)。

如果你的问题未得到解答，请通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)联系 Microsoft 支持部门。 然后选择 "**产品/服务** > " "产品 > **/服务"**"产品 **/服务"**。

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>收到新客户领导时，我收到电子邮件通知。 如何将其他人配置为接收这些电子邮件？

在合作伙伴中心访问你的产品/服务，并访问 "**产品/服务设置**" 页 > "**潜在客户管理** > "**编辑**。 更新 "**联系人电子邮件**" 字段下的电子邮件地址。

## <a name="next-steps"></a>后续步骤

完成技术设置后，将这些潜在顾客整合到当前的销售和市场营销策略以及操作流程中。 我们想要更好地了解你的总体销售过程，并想要与你密切合作，为你提供高质量的潜在顾客和足够的数据以使你取得成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果你有兴趣[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议，使你的销售团队能够更成功地利用商业市场主管，请告知我们。
