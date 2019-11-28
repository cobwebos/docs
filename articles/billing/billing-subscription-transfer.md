---
title: 将 Azure 订阅的计费所有权转移到另一帐户 | Microsoft Docs
description: 介绍如何将 Azure 订阅的计费所有权转移到另一帐户，并解答有关转移过程的一些常见问题 (FAQ)
keywords: 转移 Azure 订阅, Azure 转移订阅, 将 Azure 订阅转移到另一帐户, Azure 更改订阅所有者, 将 Azure 订阅转移到另一帐户, Azure 转移计费
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a858ef9918135bfb660c059d80f4c7c49e1e3364
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223562"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>将 Azure 订阅的计费所有权转移到另一帐户

如果你即将离职，或者希望在另一帐户中计收订阅费用，可以转移 Azure 订阅的计费所有权。 将计费所有权转移到另一帐户后，对新帐户拥有权限的管理员可以执行各种计费任务，例如更改付款方式、查看费用，以及取消订阅。

若要保留计费所有权，但要更改订阅的类型，请参阅[将 Azure 订阅切换到另一套餐](billing-how-to-switch-azure-offer.md)。 若要控制谁可以管理订阅中的资源，请参阅 [Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果你是企业协议 (EA) 客户，则你的企业管理员可以在帐户之间转移订阅的计费所有权。 有关详细信息，请参阅[转移企业协议 (EA) 订阅的计费所有权](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>转移 Azure 订阅的计费所有权

1. 以包含所要转移订阅的计费帐户的管理员身份登录到 [Azure 门户](https://portal.azure.com)。 若要确定你是否为管理员，请参阅[常见问题解答](#faq)。

1. 在“成本管理 + 计费”  中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 在左侧窗格中选择“订阅”。  根据你的访问权限，可能需要选择一个计费范围，然后选择“订阅”或“Azure 订阅”。  

1. 选择要转移的订阅对应的“转移计费所有权”。 

   ![选择要转移的订阅](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 输入充当帐户计费管理员的用户（该用户将是订阅的新所有者）的电子邮件地址。

1. 若要将订阅转移到另一个 Azure AD 租户中的帐户，请选择是否要将该订阅移到新帐户的租户。 有关详细信息，请参阅[将订阅转移到另一个 Azure AD 租户中的帐户](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > 如果选择将订阅移到新帐户的 Azure AD 租户，将会永久删除用于管理订阅中的资源的所有[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)分配。 只有新帐户中接受了转移请求的用户才有权管理订阅中的资源。 有关详细信息，请参阅[将订阅转移到另一个 Azure AD 租户中的用户](../active-directory/managed-identities-azure-resources/known-issues.md)。 或者，可以取消选中“订阅”Azure AD 租户对应的框，以便在不将订阅移到新帐户的租户的情况下转移计费所有权。 这会保留现有的管理 Azure 资源的 RBAC 权限。

    ![“发送转移请求”页](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 选择“发送转移请求”。 

1. 用户将收到一封电子邮件，其中包含查看转让请求的说明。

   ![发送给收件人的订阅转移电子邮件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要批准转移请求，用户需选择电子邮件中的链接，并按说明操作。 用户必须选择用于支付订阅费用的付款方式。 此外，如果用户没有 Azure 帐户，则他们必须注册一个新帐户。

   ![第一个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 订阅现已转让。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>将订阅转移到另一个 Azure AD 租户中的帐户

注册 Azure 时，会创建一个 Azure Active Directory (AD) 租户。 该租户代表你的帐户。 使用该租户可以管理对订阅和资源的访问权限。

创建新订阅时，该订阅将托管在帐户的 Azure AD 租户中。 若要为其他用户提供对你的订阅或其资源的访问权限，需要邀请他们加入你的租户。 这有助于控制对订阅和资源的访问。

将订阅的计费所有权转移到另一个 Azure AD 租户中的帐户时，可将订阅移到新帐户的租户。 这样，以前拥有管理订阅及其资源的[基于角色的访问权限 (RBAC)](../role-based-access-control/role-assignments-portal.md) 的用户、组或服务主体将失去其访问权限。 只有新帐户中接受了转移请求的用户才有权管理这些资源。 若要为最初拥有访问权限的用户提供访问权限，新的所有者必须[手动将这些用户添加到订阅](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>转移 Visual Studio、MPN 和即用即付开发/测试订阅

Visual Studio 和 Microsoft 合作伙伴网络订阅具有关联的每月定期 Azure 额度。 转移这些订阅时，你的额度在目标计费帐户中不可用。 订阅使用目标计费帐户中的额度。 例如，如果 Bob 在 9 月 9 日将 Visual Studio Enterprise 订阅转移到了 Jane 的帐户，而 Jane 接受了这种转移。 转移完成后，订阅将开始使用 Jane 帐户中的额度。 该额度在每个月的 9 号重置。


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>转移企业协议 (EA) 订阅的计费所有权

企业管理员可以在注册中的帐户之间转移订阅所有权。 有关详细信息，请参阅[在 EA 门户中转移帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受计费所有权后的步骤

如果你已接受 Azure 订阅的计费所有权，我们建议查看以下后续步骤：

1. 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
1. 更新与此订阅的服务关联的凭据，包括：
   1. 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)
   1. 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)
   1. Azure 虚拟机等服务的远程访问凭据。
1. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 门户](https://portal.azure.com)中更新合作伙伴 ID。 有关详细信息，请参阅[将合作伙伴 ID 链接到 Azure 帐户](billing-partner-admin-link-started.md)。

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支持的订阅类型

Azure 门户中的订阅转移适用于下面列出的订阅类型。 [免费试用](https://azure.microsoft.com/offers/ms-azr-0044p/)或 [Azure 开放许可 (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 订阅目前不支持转移。 若要暂时避开此问题，请参阅[将资源移动到新的资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。 若要转移其他订阅（例如[赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)或支持计划），请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [通过 EA 门户](#EA)。

\*\* 仅支持在 Azure 网站上注册期间创建的帐户。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>面向发送方的常见问题解答 (FAQ)

这些 FAQ 适用于将 Azure 订阅计费所有权转移到另一帐户的用户。

### <a name="whoisaa"></a> 谁是帐户的计费管理员？

计费管理员是有权管理帐户计费的人员。 他们有权在 [Azure 门户](https://portal.azure.com)中访问计费，并可执行各种计费任务，例如创建订阅、查看和支付发票，或更新付款方式。

若要确定你是哪些帐户的计费管理员，请使用以下步骤：

1. 访问 [Azure 门户中的“成本管理 + 计费”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 在左窗格中选择“所有计费范围”。 
1. 订阅页将列出你是其计费管理员的所有订阅。

如果不确定谁是订阅的帐户管理员，可使用以下步骤查明。

1. 请访问 [Azure 门户中的订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”  下的信息。
1. 选择“属性”。  订阅的帐户管理员会显示在“帐户管理员”框中。 

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有内容都会转让吗？ 包括资源组、VM、磁盘和其他正在运行的服务吗？

所有资源（例如 VM、磁盘和网站）将转移到新帐户。 但是，如果将订阅转移到了另一个 Azure AD 租户中的帐户，则订阅的任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 分配将[不会转移](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)。 此外，[应用注册](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他特定于租户的服务不会随订阅一起转移。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>是否可将所有权转移到另一个国家/地区中的帐户？
遗憾的是，在 Azure 门户中无法执行跨国家/地区的转移。 若要跨国家/地区转移订阅，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是两个帐户的管理员。 是否可将一个帐户的订阅转移到另一个帐户？
是的，可以在帐户之间转移订阅。 帐户在概念上被视为两个不同用户的帐户，因此，你可以使用上述步骤在帐户之间转移订阅。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>订阅转让会造成任何服务停机吗？

如果将订阅转移到同一 Azure AD 租户中的帐户，该订阅中正在运行的资源不受影响。 但是，如果你将订阅转移到另一个租户中的帐户，并决定将订阅移到该租户，则以前拥有管理订阅中资源的[基于角色的访问权限 (RBAC)](../role-based-access-control/overview.md) 的所有用户、组和服务主体将失去其访问权限。 这可能会导致服务停机。

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>新帐户中的用户是否有权访问使用情况和计费历史记录？

新帐户中用户可获得的唯一信息是上个月的订阅费用。 其余的使用情况与计费历史记录不会随订阅一起转移。

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何将 Azure 订阅的数据和服务迁移到新的订阅？

如果无法转让订阅所有权，可以手动迁移资源。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果我转移了 Visual Studio 或 Microsoft 合作伙伴网络订阅，我的额度是否会随订阅一起转移到新帐户？

不会，你的额度在新帐户中不可用。 接受转移请求的用户需要拥有 Visual Studio 许可证才能接受转移请求。 订阅使用用户帐户中可用的 Visual Studio 额度。 有关详细信息，请参阅[转移 Visual Studio、Microsoft 合作伙伴网络 (MPN) 和即用即付开发/测试订阅](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)。


## <a name="frequently-asked-questions-faq-for-recipients"></a>面向接收方的常见问题解答 (FAQ)

这些 FAQ 适用于从另一帐户接受 Azure 订阅计费所有权的用户。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果我从另一帐户接管了订阅的计费所有权，该帐户中的用户是否可以继续访问我的资源？

是的。 不过，如果你的帐户所在的 Azure AD 租户不同于订阅的租户，并且发送转移请求的用户将订阅移到了你的帐户的租户，则会删除任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 分配。 若要查看拥有[基于角色的访问权限 (RBAC)](../role-based-access-control/overview.md)，可管理订阅中的资源的用户，请使用以下步骤：

1. 访问 [Azure 门户中的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，然后在左窗格中选择“访问控制(IAM)”。 
1. 选择页面顶部的“角色分配”。  角色分配页将列出对订阅拥有 RBAC 访问权限的所有用户。

即使在转移过程中删除了[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 分配，原始所有者帐户中的用户仍可以通过某些安全机制访问订阅，这些机制包括：

* 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
* 存储空间等服务的访问密钥。 有关详细信息，请参阅[有关 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。
* Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其资源的访问，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

  1. 登录到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“所有资源”  。
  3. 选择资源。
  4. 在资源页中单击“设置”。  可以在这里查看和更新现有机密。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果我在计费周期的中途接管了订阅的计费所有权，是否需要支付整个计费周期的费用？

你的帐户负责支付从转移时间开始报告的任何使用费。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 使用费将包含在你帐户的帐单中。

### <a name="can-i-use-a-different-payment-method"></a>是否可以使用不同的付款方式？

是的。 在接受转移请求时，可以选择已关联到帐户的现有付款方式，也可以添加新的付款方式。

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>如果原始帐户所有者不再属于组织，我如何转让企业协议 (EA) 订阅帐户所有权？

即使原始帐户所有者不再属于组织，企业管理员也可以为任何帐户更新帐户所有权。 他们可以按照 EA 门户中[为所有订阅转让帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)的说明进行操作。

## <a name="troubleshooting"></a>故障排除

### <a id="no-button"></a> 为什么我没有看到“转让订阅”按钮？

自助式服务订阅转移不适用于你的计费帐户。 目前，我们不支持在 Azure 门户上转移企业协议 (EA) 帐户中订阅的计费所有权。 此外，在 Microsoft 代表的配合下创建的 Microsoft 客户协议帐户不支持转移计费所有权。

### <a id="no-button"></a> 为何我的订阅类型不支持转移？

并非所有类型的订阅都支持计费所有权转移。 若要查看支持转移的订阅类型列表，请参阅[支持的订阅类型](#supported-subscription-types)

### <a id="no-button"></a> 尝试转移订阅的计费所有权时，为何会出现拒绝访问错误？

如果你尝试转移 Microsoft Azure 计划订阅，但没有必要的权限，则会看到此错误。 若要转移 Microsoft Azure 计划订阅，你需是要从中计收订阅费用的发票科目的所有者或参与者。 有关详细信息，请参阅[管理发票科目的订阅](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
