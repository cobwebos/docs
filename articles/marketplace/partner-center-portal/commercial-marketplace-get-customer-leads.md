---
title: Microsoft 商业市场的潜在客户管理
description: 了解如何生成和接收 Microsoft AppSource 和 Azure Marketplace 产品/服务的潜在客户
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 89b73fd98ca773668d2eb53892d0c21397e9abf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559571"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>商业市场产品/服务的潜在客户

潜在客户是对 [Microsoft AppSource](https://appsource.microsoft.com) 和 [Azure Marketplace](https://azuremarketplace.microsoft.com) 上的产品/服务感兴趣或部署这些产品/服务的客户。 可以在产品/服务发布到商业市场后接收潜在客户。 本文解释了以下潜在客户管理概念：

* 商业市场产品/服务如何生成潜在客户，确保你不会错过商机。 
* 如何将客户关系管理 (CRM) 系统连接到产品/服务，以便在一个中心位置管理潜在客户。
* 将潜在客户数据发送给你，以便跟进与你联系的客户。

## <a name="generate-customer-leads"></a>生成潜在客户

下面是生成潜在客户的位置：

- 客户从商业市场上选择“与我联系”，同意共享其信息。 这是潜在客户的初始兴趣。 我们会与你共享有关信息，让你了解哪些客户表示有兴趣获取你的产品。 潜在顾客位于购买漏斗的顶部。

    ![Dynamics 365“与我联系”](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 客户选择“立即获取”（或者在 [Azure 门户](https://portal.azure.com/)中选择“创建”）以获取你的产品/服务。 这种潜在客户是有效客户。 我们会与你共享有关信息，让你了解哪些客户已开始部署你的产品。

    ![SQL“立即获取”按钮](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server“创建”按钮](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 客户选择“体验版”或“免费试用版”来试用你的产品/服务。 体验版或免费试用版是一个很好的促进机会，可通过它毫无障碍地与潜在客户即时分享业务相关信息。

    ![Dynamics 365 体验版按钮](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 免费试用版按钮](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>连接到 CRM 系统

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>了解潜在客户数据

在客户购买过程中收到的每个潜在顾客都在特定的字段中有数据。 要查找的第一个字段是 `LeadSource` 字段，其格式如下：Source-Action | Offer。

**来源**：系统将根据生成潜在客户的市场填充此字段的值。 可能值为 `"AzureMarketplace"`、`"AzurePortal"` 和 `"AppSource (SPZA)"`。

**操作**：系统会根据客户在生成潜在客户的市场中执行的操作来填充此字段的值。

可能的值包括：

- "INS"：表示安装。 当客户获取你的产品时，将在 Azure 市场或 AppSource 中执行此操作。
- "PLT"：表示合作伙伴引导的试用。 当客户选择“与我联系”选项时，将在 AppSource 中执行此操作。
- "DNC"：表示请勿联系。 请求与应用页面上交叉列出的合作伙伴联系时，将在 AppSource 中执行此操作。 我们共享以下通知：此客户已在应用上交叉列出，但不需要联系他们。
- "Create"：此操作只能在 Azure 门户中执行，它是在客户通过其帐户购买你的产品/服务时生成的。
- "StartTestDrive"：此操作仅适用于“体验版”选项，它是在客户启动其体验版时生成的。

**产品/服务**：你在商业市场可能有多个产品/服务。 系统根据生成潜在客户的产品/服务填充此字段的值。 发布者 ID 和产品/服务 ID 都会发送到此字段，是你在将产品/服务发布到市场时提供的值。

以下示例以所需的格式 `publisherid.offerid` 显示出值： 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>客户信息

客户信息是通过多个字段发送的。 以下示例显示某个潜在客户包含的客户信息：

- 名：John
- 姓：Smith
- 电子邮件：jsmith\@microsoft.com
- 电话：1234567890
- 国家/地区：美国
- 公司：Microsoft
- 标题：CTO

>[!NOTE]
>并非以上示例中的所有数据都始终适用于每个潜在顾客。 由于将从“生成潜在客户”部分中提到的多个步骤中获取潜在客户，因此处理潜在客户的最佳方式是删除重复记录并使后续记录个性化。 这样，每个客户都将收到合适的消息，并且你可以创建独特的关系。

## <a name="best-practices-for-lead-management"></a>潜在客户管理的最佳做法

下面是通过销售周期推动潜在顾客的一些建议：

- **过程**：使用里程碑、分析和明确的团队所有权定义一个明确的销售过程。
- 资格：定义先决条件，用于指示潜在客户是否完全符合资格。 请确保销售或营销代表先仔细鉴定潜在客户资格，再引导潜在客户完成整个销售流程。
- **跟进**：不要忘记在24小时内跟进。 在客户部署了测试驱动器后，你将立即在你的 CRM 中获得潜在顾客;在仍处于热情的情况下将其发送给他们。 请求计划电话呼叫，以便更好地了解你的产品是否是解决问题的好办法。 预计典型事务需要大量跟进调用。
- 巩固关系：培养潜在客户，助你获得更多利润。 签入，但不要 bombard 它们。 建议你在关闭之前至少经过几次电子邮件：第一次尝试后不放弃。 请记住，这些客户直接参与您的产品，并在免费试用中花费时间;它们是很棒的客户。

## <a name="common-questions-about-lead-management"></a>有关潜在客户管理的常见问题

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪里获取有关设置潜在客户目标的帮助？

按照[连接到 CRM 系统](#connect-to-your-crm-system)部分中的步骤进行操作，或通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)提交支持票证。 然后选择“产品/服务创建” > “产品/服务的类型” > “潜在客户管理配置”。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>是否必须在配置潜在客户目标后，才能在商业市场上发布产品/服务？

答案取决于要发布的产品/服务的类型。 软件即服务 (SaaS) 和 Dynamics 365 Customer Engagement 使用“与我联系”列出所有 Dynamics 365 for Finance and Operations 产品/服务、所有 Dynamics 365 Business Central 产品/服务以及所有咨询服务的产品/服务。 因此，它们需要与潜在客户目标建立连接。 如果你的产品/服务类型未列出，则不需要与潜在客户目标建立连接。 建议你配置潜在客户目标，以免错过商机。

### <a name="how-can-i-find-the-test-lead"></a>如何查找测试潜在客户？

在潜在客户目标中搜索 `"MSFT_TEST"`。 下面是 Microsoft 的测试潜在客户示例：

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有实时产品/服务，但是为什么看不到任何潜在客户？

请确保与潜在客户目标的连接有效。 在合作伙伴中心选择和产品/服务对应的“发布”后，我们将向你发送测试潜在客户。 如果看到测试潜在客户，则连接有效。 还可以通过在预览步骤中尝试获取产品/服务预览版来测试潜在客户连接。 在商业市场的列表中选择“立即获取”、“与我联系”或“免费试用版”。

此外，请确保正在查找正确的数据。 本文的[了解潜在客户数据](#understand-lead-data)部分介绍了发送到潜在客户目标的潜在客户数据。

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已将 Azure Blob 存储配置为我的潜在客户目标，但为什么看不到潜在客户？

目前已不再支持将 Azure Blob 存储作为潜在客户目标，因此你会错过你的产品/服务生成的任何潜在客户。 切换到任何其他[潜在客户目标选项](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到了来自商业市场的电子邮件，但为什么在 CRM 中找不到潜在客户？

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会传递 .edu 域中的个人信息。 请通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)提交支持票证。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已将 Azure 表配置为我的潜在客户目标。 如何查看潜在客户？

可以从 Azure 门户访问存储在 Azure 表中的潜在客户数据。 也可以免费下载和安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 存储帐户的表数据。

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我已将 Azure 表配置为我的潜在客户目标。 在系统发送新的商业市场潜在客户时，我是否能收到通知？

是的。 按照[使用 Azure 表配置潜在客户管理](./commercial-marketplace-lead-management-instructions-azure-table.md)中的说明进行操作以设置 Microsoft flow，用于在将潜在客户添加到 Azure 表时发送电子邮件。

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我已将 Salesforce 配置为潜在客户目标，但为什么找不到潜在客户？

检查潜在 web-to-lead 表单是否是基于选择列表的必填字段。 如果是，请将该字段切换为非必填文本字段。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>潜在客户目标存在问题，我丢失了一些潜在客户。 可以通过电子邮件将他们发送给我吗？

由于个人信息政策，我们无法通过不安全的电子邮件共享潜在客户信息。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已将 Azure 表配置为我的潜在客户目标。 它的成本是多少？

潜在客户生成数据量很低。 对于几乎所有发布者都小于 1 GB。 具体成本取决于接收到的潜在客户数量。 例如，如果每个月收到 1,000 个潜在客户，则成本大约为 50 美分。 有关存储定价的详细信息，请参阅 [Azure 存储概述 - 定价](https://azure.microsoft.com/pricing/details/storage/)。

如果你的问题未得到解答，请通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)联系 Microsoft 支持部门。 然后选择“产品/服务创建” > 产品/服务的类型” > “潜在客户管理配置”。

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>收到新的潜在客户时，我还收到电子邮件通知。 如何将其他人配置为这些电子邮件的接收者？

在合作伙伴中心访问你的产品/服务，进入“产品/服务设置”页>“潜在客户管理” > “编辑”。 更新“联系人电子邮件”字段下的电子邮件地址。

## <a name="next-steps"></a>后续步骤

完成技术性设置后，应当将这些潜在客户纳入到当前的销售和市场营销策略以及操作流程中。 我们希望更好地了解整个销售流程，还希望更紧密地与你合作来提供高质量的潜在客户和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果想要[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议来使销售团队借助商业市场潜在客户信息取得更大的成功，请告知我们。
