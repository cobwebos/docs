---
title: 开始使用更新的 Azure 计费帐户
description: 开始使用更新的 Azure 计费帐户，了解新的计费和成本管理体验中的更改
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 27a6f8f0ed0d58deb64f768d2e47bafebc910edb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690419"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>开始使用更新的 Azure 计费帐户

管理成本和发票是云体验的关键组成部分之一。 它可帮助你控制和了解云上的支出。 为了使你更轻松地管理成本和发票，Microsoft 正在更新 Azure 计费帐户，以加入增强的成本管理和计费功能。 本文介绍了对计费帐户的更新，并介绍了一些新功能。

> [!IMPORTANT]
> 当你收到 Microsoft 发出的电子邮件，通知你有关帐户更新的信息时，你的帐户将获得更新。 通知将在你的帐户更新前 60 天发送。

## <a name="more-flexibility-with-your-new-billing-account"></a>新计费帐户具有更大的灵活性

下图比较了新旧计费帐户：

![该图显示了新旧帐户中计费层次结构之间的比较](./media/mosp-new-customer-experience/comparison-old-new-account.png)

新的计费帐户包含一个或多个计费对象信息，可用于管理发票和付款方式。 每个计费配置文件包含一个或多个发票科目，用于在计费配置文件的发票上组织成本。

![该图显示了新的计费层次结构](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

计费帐户上的角色具有最高级别的权限。 这些角色应分配给那些需要查看发票并跟踪整个帐户的成本的用户，例如组织中的财务或 IT 经理或注册帐户的个人。 有关详细信息，请参阅[计费帐户角色和任务](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)。 帐户更新后，在旧计费帐户中具有帐户管理员角色的用户将在新帐户中被赋予所有者角色。

## <a name="billing-profiles"></a>计费配置文件

计费配置文件用于管理发票和付款方式。 每月发票是在当月开始时针对帐户中的每项计费配置文件生成的。 发票包含上月与计费配置文件相关联的所有订阅的相应费用。

帐户更新后，将自动为每个订阅创建一个计费配置文件。 订阅费用将按其各自的计费配置文件计费，并显示在其发票上。

计费配置文件中的角色有权查看和管理发票和付款方式。 这些角色应分配给为发票付款的用户，例如组织中会计团队的成员。 有关详细信息，请参阅[计费配置文件角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。 

帐户更新后，对于你已授予其他人[查看发票](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice)权限的每个订阅，具有所有者、参与者、读者或计费读者 Azure RBAC 角色的用户将在相应的计费配置文件中被赋予读者角色。

## <a name="invoice-sections"></a>发票科目

发票科目用于组织发票中的成本。 例如，你可能需要单张发票，但同时需要按部门、团队或项目组织成本。 对于这种情况，可以设置单项计费配置文件，在其中为每个部门、团队或项目创建发票科目。

帐户更新后，将为每个计费配置文件创建一个发票科目，并将相关订阅分配给该发票科目。 添加更多订阅后，可以创建额外的科目并将订阅分配给发票科目。 你将看到此计费配置文件发票上的这些科目反映了你分配给它们的每个订阅的使用情况。

发票科目的角色有权控制谁来创建 Azure 订阅。 这些角色应分配给在组织中为团队设置 Azure 环境的用户，例如工程主管和技术架构师。 有关详细信息，请参阅[发票科目角色和任务](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="enhanced-features-in-your-new-experience"></a>新体验中的增强功能

你的新体验包括以下成本管理和计费功能，这些功能使你可以轻松管理成本和发票：

#### <a name="invoice-management"></a>发票管理

**每月计费周期更容易预测** - 在新帐户中，无论你何时注册使用 Azure，计费周期都从该月的第一天开始，到该月的最后一天结束。 发票将在每月月初生成，其中包含上个月的所有费用。

**为多个订阅获取单张月度发票** - 你可以灵活地为每个订阅获取一张月度发票，也可以为多个订阅获取单张发票。

**收到包含 Azure 订阅、支持计划和 Azure 市场产品的单张月度发票** - 你将获得一张包含所有费用（包括 Azure 订阅的使用费用、支持计划费用和 Azure 市场购买费用）的月度发票。

**根据你的需求对发票上的成本进行分组** - 你可以根据需求（按部门、项目或团队）对发票上的费用进行分组。

**在发票上设置可选的采购订单号** - 为了将发票与内部财务系统相关联，请设置采购订单号。 在 Azure 门户中可以随时进行管理和更新。

#### <a name="payment-management"></a>付款管理

**使用信用卡立即支付发票** - 无需等待从你的信用卡中自动扣除费用。 你可以在 Azure 门户中使用任意信用卡支付到期发票或逾期未付发票。

**跟踪应用于该帐户的所有付款** - 在 Azure 门户中可以查看应用于你的帐户的所有付款，包括使用的付款方式、已付款的金额和付款日期。

#### <a name="cost-management"></a>成本管理

**计划每月将使用情况数据导出到存储帐户** - 每天、每周或每月将你的成本和使用情况数据自动发布到存储帐户一次。

#### <a name="account-and-subscription-management"></a>帐户和订阅管理

**分配多个管理员执行计费操作** - 将计费权限分配给多个用户以管理帐户的帐单。 通过向其他人提供读取和/或写入权限来获得灵活性。

**在 Azure 门户中直接创建其他订阅** - 只需在 Azure 门户中单击一次即可创建所有订阅。

#### <a name="api-support"></a>API 支持

**通过 API、SDK 和 PowerShell 执行计费和成本管理操作** - 使用成本管理、计费和消耗 API 将计费和成本数据提取到你首选的数据分析工具中。

**通过 API、SDK 和 PowerShell 执行所有订阅操作** - 使用 Azure 订阅 API 来自动管理 Azure 订阅，包括创建、重命名和取消订阅。

## <a name="get-prepared-for-your-new-experience"></a>为新体验做好准备

为使你能够为新体验做好准备，我们提出了以下建议：

**月度计费周期和不同的发票日期**

在新体验中，发票将在每个月的第 9 天左右生成，其中包含上个月的所有费用。 此日期可能与在旧帐户中生成发票的日期不同。 如果你与其他人共享你的发票，请通知他们有关此日期更改的事宜。

**新的计费和成本管理 API**

如果你正使用成本管理或计费 API 来查询和更新计费或成本数据，则必须使用新 API。 下表列出了不能用于新计费帐户的 API，以及你需要在新计费帐户中进行的更改。

|API | 更改  |
|---------|---------|
|[计费帐户 - 列表](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/billingaccounts/list) | 在计费帐户 - 列表 API 中，你的旧计费帐户具有 agreementType“MicrosoftOnlineServiceProgram”，而你的新计费帐户具有 agreementType“MicrosoftCustomerAgreement” 。 如果你依赖于 agreementType，请进行更新。 |
|[发票 - 按计费订阅列出](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | 此 API 将仅返回在更新帐户之前生成的发票。 必须使用[发票 - 按计费订阅列出](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API 才能获取在新计费帐户中生成的发票。 |

## <a name="additional-information"></a>其他信息

以下部分提供有关新体验的其他信息。

**无服务故障时间** 订阅中的 Azure 服务将保持正常运行，而不会中断。 仅对你的计费体验进行更新。 这不会对现有的资源、资源组或管理组产生影响。

**不会更改 Azure 资源** 对使用 Azure RBAC（基于角色的访问控制）设置的 Azure 资源的访问权限不受此更新的影响。

**过去的发票在新体验中可用** 更新帐户前生成的发票在 Azure 门户中仍然可用。

**月中更新帐户的发票** 如果你的帐户在月中更新，则你将收到一张显示累计到帐户更新当天为止的费用的发票。 你将在该月的剩余时间内收到另一张发票。 例如，你的帐户有一个订阅，该订阅在 9 月 15 日更新。 你将收到一张显示累计到 9 月 15 日为止的费用的发票。 你将在 9 月 15 日到 9 月 30 日之间收到另一张发票。 9 月份以后，你每月均会收到一张发票。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关计费帐户的详细信息。

- [了解新计费帐户的管理角色](../manage/understand-mca-roles.md)
- [为新计费帐户创建其他 Azure 订阅](../manage/create-subscription.md)
- [在发票上创建科目以管理成本](../manage/mca-section-invoice.md)
