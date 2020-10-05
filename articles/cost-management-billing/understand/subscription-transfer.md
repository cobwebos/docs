---
title: 关于转移 Azure 订阅的计费所有权
description: 本文介绍了你在将某 Azure 订阅的计费所有权转移到另一帐户之前需要了解的事项。
keywords: 转移 Azure 订阅, Azure 转移订阅, 将 Azure 订阅转移到另一帐户, Azure 更改订阅所有者, 将 Azure 订阅转移到另一帐户, Azure 转移计费
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 237565a7b72c3317e2c443f86965634ed7c9942c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336844"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>关于转移 Azure 订阅的计费所有权

本文可帮助你了解在将某 Azure 订阅的计费所有权转移到另一帐户之前应注意的事项。 

如果你即将离职，或者希望在另一帐户中计收订阅费用，可以转移 Azure 订阅的计费所有权。 将计费所有权转移到另一帐户可为新帐户中的管理员提供执行各种计费任务的权限。 他们可以更改付款方式、查看费用，以及取消订阅。

若要保留计费所有权，但要更改订阅的类型，请参阅[将 Azure 订阅切换到另一套餐](../manage/switch-azure-offer.md)。 若要控制谁可以访问订阅中的资源，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

如果你是企业协议 (EA) 客户，则你的企业管理员可在帐户之间转移订阅的计费所有权。

只有帐户的计费管理员可转移订阅的所有权。

## <a name="determine-account-billing-administrator"></a>确定帐户计费管理员

<a name="whoisaa"></a>

计费管理员是有权管理帐户计费的人员。 他们有权在 [Azure 门户](https://portal.azure.com)中访问计费，并可执行各种计费任务，例如创建订阅、查看和支付发票，或更新付款方式。

若要确定你是哪些帐户的计费管理员，请访问 [Azure 门户中的“成本管理 + 计费”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。 然后，在左窗格中选择“所有计费范围”。 订阅页面显示了你是其中的计费管理员的全部三个订阅。

如果不确定谁是订阅的帐户管理员，请访问 [Azure 门户中的订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 然后，选择要检查的订阅，并查看“设置”下的信息。 选择“属性”，“帐户管理员”框中会显示订阅的帐户管理员 。


## <a name="supported-subscription-types"></a>支持的订阅类型

Azure 门户中的订阅转移适用于下面列出的订阅类型。 [免费试用](https://azure.microsoft.com/offers/ms-azr-0044p/)或 [Azure 开放许可 (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 订阅目前不支持转移。 若要暂时避开此问题，请参阅[将资源移动到新的资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 若要转移其他订阅（例如支持计划），请联系 [Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> 使用 EA 门户。

<sup>2</sup> 仅支持在 Azure 网站上注册期间创建的帐户。

## <a name="resources-transferred-with-subscriptions"></a>与订阅一起转移的资源

所有资源（例如 VM、磁盘和网站）将转移到新帐户。 但是，如果将订阅转移到了另一个 Azure AD 租户中的帐户，则订阅的任何[管理员角色](../manage/add-change-subscription-administrator.md)和 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)都不会转移。 此外，[应用注册](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他特定于租户的服务不会随订阅一起转移。

## <a name="transfer-account-ownership-to-another-countryregion"></a>将帐户所有权转移到其他国家/区域

遗憾的是，不可使用 Azure 门户跨国家/区域转移订阅。 不过，如果你打开 Azure 支持请求，则可转移它们。 若要创建支持请求，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="transfer-a-subscription-from-one-account-to-another"></a>将订阅从一个帐户转移到另一帐户

如果你是两个帐户的管理员，则可在帐户之间转移订阅。 你的帐户在概念上被视为两个不同用户的帐户，因此你可在帐户之间转移订阅。
若要查看转移订阅所需的步骤，请参阅[转移 Azure 订阅的计费所有权](../manage/billing-subscription-transfer.md)。

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>转移订阅不得造成停机

如果将订阅转移到同一 Azure AD 租户中的帐户，该订阅中正在运行的资源不受影响。 但是，保存在 PowerShell 中的上下文信息不会更新，因此，可能必须清除此信息，或更改设置。 如果将订阅转移到另一个租户中的帐户，并决定将订阅移到该租户，则拥有 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)以访问订阅中资源的所有用户、组和服务主体都将失去访问权限。 可能会导致服务停机。

## <a name="new-account-usage-and-billing-history"></a>新帐户的使用情况和帐单

新帐户中用户可获得的唯一信息是你的订阅的上个月的费用。 其余使用情况与帐单不会随订阅一起转移。

## <a name="manually-migrate-data-and-services"></a>手动迁移数据和服务

转移订阅时，其资源会一并转移。 如果无法转移订阅所有权，可手动迁移其资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

## <a name="remaining-subscription-credits"></a>剩余订阅额度 

如果有 Visual Studio 或 Microsoft 合作伙伴网络订阅，则可获得每月额度。 你的额度不会随订阅一起转移到新帐户中。 接受转移请求的用户需要拥有 Visual Studio 许可证才能接受转移请求。 订阅使用用户帐户中可用的 Visual Studio 额度。 有关详细信息，请参阅[转移 Visual Studio 和合作伙伴网络订阅](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)。

## <a name="users-keep-access-to-transferred-resources"></a>用户仍可访问已转移的资源

请注意，在所有权转移后，在订阅中有权访问资源的用户仍具有访问权限。 但是，可能会删除[管理员角色](../manage/add-change-subscription-administrator.md)和 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)。 如果你的帐户所在的 Azure AD 租户不同于订阅的租户，并且发送转移请求的用户将订阅移到了你的帐户的租户，则会发生访问权限丢失。 

可在 Azure 门户中查看分配有 Azure 角色可访问订阅中的资源的用户。 访问 [Azure 门户中的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 然后，选择要检查的订阅，再在左窗格中选择“访问控制(IAM)”。 接下来，选择页面顶部的“角色分配”。 角色分配页将列出对订阅拥有访问权限的所有用户。

即使在转移过程中删除了 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，原始所有者帐户中的用户仍可以通过其他安全机制访问订阅，这些机制包括：

* 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../../cloud-services/cloud-services-certs-create.md)。
* 存储空间等服务的访问密钥。 有关详细信息，请参阅[有关 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。
* Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对资源的访问，他们应考虑更新与服务关联的任何机密。 大多数资源都可更新。 登录 [Azure 门户](https://portal.azure.com)，在中心菜单上选择“所有资源”。 接下来，选择资源。 然后，在资源页中选择“设置”。 可在此处查看和更新现有机密。

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>获得所有权后需对使用量付费

你的帐户负责支付从转移时间开始报告的任何使用费。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 使用费将包含在你帐户的帐单中。

## <a name="use-a-different-payment-method"></a>使用其他付款方式

在接受转移请求时，可选择已关联到帐户的现有付款方式，也可添加新的付款方式。

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>转移企业协议订阅所有权

即使原始帐户所有者不再属于组织，企业管理员也可为任何帐户更新帐户所有权。 要详细了解如何转移 Azure 企业协议帐户，请参阅 [Azure Enterprise 转移](../manage/ea-transfers.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [转移 Azure 订阅的计费所有权](../manage/billing-subscription-transfer.md)
