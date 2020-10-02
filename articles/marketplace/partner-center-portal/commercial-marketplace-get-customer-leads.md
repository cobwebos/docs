---
title: Microsoft 商业市场的潜在客户管理
description: 了解如何生成和接收 Microsoft AppSource 和 Azure Marketplace 产品/服务的潜在客户
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 5627d6afab22defebeebd3f04695ef95390f1163
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653309"
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

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

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

完成技术性设置后，应当将这些潜在客户纳入到当前的销售和市场营销策略以及操作流程中。 我们希望更好地了解整个销售流程，还希望更紧密地与你合作来提供高质量的潜在客户和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果想要[提供反馈](mailto:AzureMarketOnboard@microsoft.com)和建议来使销售团队借助商业市场潜在客户信息取得更大的成功，请告知我们。

## <a name="next-steps"></a>后续步骤

- [潜在客户管理常见问题和故障排除](../lead-management-faq.md)