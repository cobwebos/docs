---
title: 配置潜在客户 |Azure Marketplace
description: 在云合作伙伴门户中配置潜在顾客。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3e483a33d986eff767b3529e30208319ad90b23
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934989"
---
<a name="get-customer-leads"></a>获取潜在客户
==================

本文介绍如何使用云合作伙伴门户创建潜在客户。 可将这些潜在顾客连接到 CRM 系统，并将其集成到销售管道。

## <a name="leads"></a>潜在顾客

潜在顾客是指对你的产品感兴趣，或者正在从 [Azure 市场](https://azuremarketplace.microsoft.com/)或 [AppSource](https://appsource.microsoft.com) 部署你的产品的客户。

### <a name="azure-marketplace"></a>Azure 市场

1.  客户获取套餐的“体验版”。 体验版是一个很好的促进机会，可通过它毫无障碍地与潜在客户即时分享业务。 所有体验版将为希望更深入地试用你的产品的客户生成潜在顾客。 可以在 [Azure 市场体验版](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)中详细了解体验版。

    ![市场体验版示例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. 客户选择“立即获取”即表示同意共享其信息。 此潜在顾客是表达了**初始兴趣**的潜在顾客，我们将共享已表示有兴趣购买产品并且的客户的信息。 潜在顾客位于购买漏斗的顶部。

   ![“立即获取”选项](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. 客户在 [Azure 门户](https://portal.azure.com/)中选择“购买”以购买你的产品。 此潜在顾客是一个**活跃的**潜在顾客，我们将共享已开始部署产品的客户的信息。

   ![“购买”选项](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  客户获取了产品的“体验版”。 体验版是一个很好的促进机会，可通过它毫无障碍地与潜在客户即时分享业务。 所有体验版都将生成希望更深入地尝试产品的客户的潜在顾客。 可以从 [AppSource 体验版](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)了解有关体验版的详细信息。

    ![体验版示例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  客户选择“立即获取”即表示同意共享其信息。 此潜在顾客是表达了**初始兴趣**的潜在顾客，我们将共享已表示有兴趣购买产品并且的客户的信息。 潜在顾客位于购买漏斗的顶部。

      ![“立即获取”选项](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  客户在套餐中选择“与我联系”。 此潜在顾客是**活跃的**潜在顾客，我们将共享已要求跟进产品的客户的信息。

    ![“与我联系”选项](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>销售线索数据
---------

在客户购买过程中收到的每个潜在顾客都在特定的字段中有数据。 由于从多个步骤获取潜在顾客，因此处理潜在顾客的最佳方式是删除重复信息并使后续跟进个性化。 这样，每个客户都将收到合适的消息，并且你可以创建独特的关系。

### <a name="lead-source"></a>查找源

潜在顾客源的格式为**源**-**操作** |  **套餐**

**来源**："AzureMarketplace"、"AzurePortal"、"TestDrive"和"AppSource (SPZA)"

**操作**：
- "INS" -- 安装。 当客户购买你的产品时，将在 Azure 市场或 AppSource 中执行此操作。
- "PLT" – 表示合作伙伴引导的试用。 当客户使用“与我联系”选项时，将在 AppSource 中执行此操作。
- "DNC" - 不要联系。 请求与应用页面上交叉列出的合作伙伴联系时，将在 AppSource 中执行此操作。 我们共享以下提醒：此客户已在应用上交叉列出，但他们不需要取得联系。
- "Create" - 此操作只能在 Azure 门户中执行，它是在客户通过其帐户购买你的套餐时生成的。
- "StartTestDrive" - 此操作仅适用于体验版，它是在客户启动其体验版时生成的。

**套餐**

下面的示例显示分配给发布服务器和特定的产品/服务的唯一标识符： checkpoint.check-点-r77-10sg-byol、 bitnami.openedxcypress 和 docusign.3701c77e-1cfa-4 核 56 91e6 3ed0b622145a。


### <a name="customer-info"></a>客户信息

以下示例中的字段显示某个潜在顾客包含的客户信息。
- FirstName:John
- LastName:Smith
- 电子邮件：jsmith\@microsoft.com
- 电话:1234567890
- 国家/地区:美国
- 公司：Microsoft
- 标题：CTO

>[!Note]
>并非以上示例中的所有数据都始终适用于每个潜在顾客。

我们正在积极改进销售线索，因此，如果没有看到希望具有的数据字段，请[向我们发送反馈](mailto:AzureMarketOnboard@microsoft.com)。

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>如何将 CRM 系统与云合作伙伴门户进行连接
------------------------------------------------------------

为帮助你获取潜在顾客，我们已在云合作伙伴门户中构建了潜在顾客管理连接器，以便可以轻松插入 CRM 信息，并且我们将创建连接。 现在，可以轻松利用由市场生成的销售线索，而不需要执行重大的工程工作来与外部系统进行集成。

![潜在顾客管理连接器](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

我们可以将潜在顾客写入到各种 CRM 系统中或直接写入到 Azure 存储表（可以在其中以你喜欢的任何方式管理潜在顾客）。 以下每个链接提供了有关如何连接每个可能的潜在顾客目标的说明：

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) 提供了有关如何配置 Dynamics CRM Online 以获取潜在顾客的说明。
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) 提供了有关如何设置 Marketo 潜在顾客配置以获取潜在顾客的说明。
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) 提供了有关设置 Salesforce 实例以获取潜在顾客的说明。
-    [Azure 表](./cloud-partner-portal-lead-management-instructions-azure-table.md)提供了有关设置 Azure 存储帐户以在 Azure 表中获取潜在顾客的说明。
-   [Https 终结点](./cloud-partner-portal-lead-management-instructions-https.md)提供了有关设置 Https 终结点以获取潜在顾客的说明。

配置潜在顾客目标并发布套餐后，我们将验证连接，并发送一个测试潜在顾客。 如果要在投入使用前查看产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试销售线索连接。 请务必确保潜在顾客设置保持最新状态，以便你不会错失任何潜在顾客，因此，每当终端发生了更改时都务必更新这些连接。

<a name="what-next"></a>接下来要做什么？
----------

在技术性设置到位后，应当将这些销售线索纳入到当前的销售和市场营销策略以及操作流程中。 我们非常希望更好地了解整个销售流程并希望更紧密地与你合作来提供高质量的销售线索和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果想要[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议来使销售团队借助市场潜在顾客取得更大的成功，请告知我们。
