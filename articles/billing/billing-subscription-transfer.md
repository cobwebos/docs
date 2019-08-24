---
title: 将 Azure 订阅的计费所有权转让给其他帐户 |Microsoft Docs
description: 描述如何将 Azure 订阅的计费所有权转让给另一个帐户, 以及有关该过程的一些常见问题 (FAQ)
keywords: 传输 azure 订阅, azure 传输订阅, 将 azure 订阅移动到另一个帐户, azure 更改订阅所有者, 将 azure 订阅转移到其他帐户, azure 传输计费
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012548"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>将 Azure 订阅的计费所有权转让给其他帐户

如果你离开你的组织, 或者你希望订阅按其他帐户计费, 则你可能需要转让 Azure 订阅的计费所有权。 将帐单所有权转移到另一个帐户后, 管理员可以在 "新建帐户" 中执行计费任务, 如更改付款方式、查看费用, 以及取消订阅。

如果要保留计费所有权但更改订阅类型, 请参阅将[Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。 如果要控制哪些用户可以管理订阅中的资源, 请参阅[Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果你是企业协议 (EA) 客户, 你的企业管理员可以在帐户之间传输订阅的计费所有权。 有关详细信息, 请参阅[转让企业协议 (EA) 订阅的计费所有权](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>转让 Azure 订阅的计费所有权

1. 以具有要传输的订阅的计费帐户的管理员身份登录到[Azure 门户](https://portal.azure.com)。 若要了解你是否是管理员, 请参阅[常见问题](#faq)。

1. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 在左侧窗格中选择 "**订阅**"。 根据你的访问权限, 可能需要选择计费范围, 然后选择 "**订阅**" 或 " **Azure 订阅**"。

1. 选择要传输的订阅的 "**传输帐单所有权**"。 

   ![选择要传输的订阅](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 输入用户的电子邮件地址, 该用户是将作为订阅的新所有者的帐户的管理员。

1. 如果要将订阅转移到另一个 Azure AD 租户中的帐户, 请选择是否要将订阅移到新帐户的租户。 有关详细信息, 请参阅[将订阅转移到另一个 Azure AD 租户中的帐户](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > 如果选择将订阅移到新帐户的 Azure AD 租户, 则将永久删除用于管理订阅中的资源的所有[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)分配。 只有接受你的传输请求的新帐户中的用户才有权管理订阅中的资源。 有关详细信息, 请参阅[将订阅转移到另一个 Azure AD 租户中的用户](../active-directory/managed-identities-azure-resources/known-issues.md)。 或者, 可以取消选中 "订阅 Azure AD 租户" 框, 以便在不将订阅移动到新帐户的租户的情况下传输计费所有权。 如果这样做, 将维护现有的用于管理 Azure 资源的 RBAC 权限。
  
    ![发送传输页](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 选择 "**发送传输请求**"。

1. 用户将收到一封电子邮件, 其中包含查看传输请求的说明。

   ![发送给收件人的订阅转让电子邮件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要批准传输请求, 用户需要选择电子邮件中的链接, 然后按照说明进行操作。 用户必须选择将用于支付订阅费用的付款方式。 此外, 如果用户没有 Azure 帐户, 则需要注册新帐户。 

   ![第一个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 订阅现已转让。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>将订阅传输到另一个 Azure AD 租户中的帐户

当你注册 Azure 时, 将为你创建一个 Azure Active Directory (AD) 租户。 租户表示你的帐户。 使用租户管理对订阅和资源的访问权限。

当你创建新订阅时, 它将托管在你的帐户的 Azure AD 租户中。 如果要向其他人提供对订阅或其资源的访问权限, 则需要邀请他们加入你的租户。 这可以帮助你控制对订阅和资源的访问权限。

将订阅的计费所有权转移到另一个 Azure AD 租户中的帐户时, 可以将订阅移到新帐户的租户。 如果这样做, 则所有具有[基于角色的访问权限 (RBAC)](../role-based-access-control/role-assignments-portal.md)的用户、组或服务主体都将失去访问权限。 只有接受你的传输请求的新帐户中的用户才有权管理资源。 若要提供对最初拥有访问权限的用户的访问权限, 新所有者必须[手动将这些用户添加到订阅](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>传输 Visual Studio、MPN 和即用即付开发/测试订阅

Visual Studio 和 Microsoft 合作伙伴网络订阅都具有与之关联的每月定期 Azure 信用额度。 当你转让这些订阅时, 你的信用额度在目标计费帐户中不可用。 订阅在目标计费帐户中使用该信用额度。 例如, 如果 Bob 在9月9日将 Visual Studio Enterprise 订阅传输到 Jane 的帐户, 则 Jane 接受此传输。 传输完成后, 订阅会开始使用 Jane 帐户中的信用额度。 信用额度将于每个月的第9次重置。 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>转让企业协议 (EA) 订阅的计费所有权

企业管理员可以在注册中的帐户之间转移订阅的所有权。 有关详细信息, 请参阅在 EA 门户中[传输帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受计费所有权后的后续步骤

如果已接受 Azure 订阅的计费所有权, 则建议你查看以下后续步骤:

1. 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
1. 更新与此订阅的服务关联的凭据，包括：
   1. 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)
   1. 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)
   1. Azure 虚拟机等服务的远程访问凭据。
1. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 门户](https://portal.azure.com)中更新合作伙伴 ID。 有关详细信息, 请参阅将[合作伙伴 ID 链接到 Azure 帐户](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支持的订阅类型

Azure 门户中的订阅转让适用于下面列出的订阅类型。 对于[免费试用](https://azure.microsoft.com/offers/ms-azr-0044p/)或[Azure 开放许可 (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/)订阅, 不支持当前传输。 若要暂时避开此问题，请参阅[将资源移动到新的资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。 若要转让其他订阅, 例如[赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)或支持计划, 请[联系 Azure 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

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

\*[通过 EA 门户](#EA)。

\*\*仅支持在 Azure 网站上注册过程中创建的帐户。 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>发件人常见问题 (FAQ)

这些 Faq 适用于将 Azure 订阅的计费所有权转让给其他帐户的用户。

### <a name="whoisaa"></a>谁是帐户的帐单管理员？

帐单管理员是有权管理帐户计费的人员。 他们有权访问[Azure 门户](https://portal.azure.com)上的计费, 并执行各种计费任务, 例如创建订阅、查看和支付发票或更新付款方式。

若要确定作为计费管理员的帐户, 请使用以下步骤:

1. 访问[Azure 门户中的 "成本管理 + 计费" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 从左侧窗格中选择 "**所有计费范围**"。 
1. "订阅" 页将列出您为其计费管理员的所有订阅。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有内容都会转让吗？ 包括资源组、VM、磁盘和其他正在运行的服务吗？

所有资源 (例如 Vm、磁盘和网站) 都会传输到新帐户。 但是, 如果将订阅转移到另一个 Azure AD 租户中的帐户, 则订阅上的任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)分配都[不会传输](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)。 此外,[应用注册](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他特定于租户的服务不会随订阅一起传输。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>是否可以将所有权转移到另一个国家/地区的帐户？
遗憾的是, 无法在 Azure 门户中执行跨国家/地区的传输。 若要跨国家/地区转让订阅,[请联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是两个帐户的管理员。 是否可以将订阅从我的一个帐户转移到另一个帐户？
是的, 你可以在帐户之间传输订阅。 你的帐户在概念上被视为两个不同用户的帐户, 因此你可以使用上述步骤来传输帐户之间的订阅。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>订阅转让会造成任何服务停机吗？

如果将订阅转让给同一 Azure AD 租户中的帐户, 则不会影响订阅中运行的资源。 但是, 如果将订阅转移到另一个租户中的帐户, 并决定将订阅移动到租户, 则具有[基于角色的访问权限 (RBAC)](../role-based-access-control/overview.md)的所有用户、组和服务主体将失去其访问权限. 这可能会导致服务停机。

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>新帐户中的用户是否有权访问使用情况和计费历史记录？

新帐户中的用户只能使用的信息是上个月的订阅费用。 其他使用情况和计费历史记录不会与订阅一起传输

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何将 Azure 订阅的数据和服务迁移到新的订阅？

如果无法转让订阅所有权，可以手动迁移资源。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果我转移了 Visual Studio 或 Microsoft 合作伙伴网络订阅, 则我的信用额度是否会在新帐户中继续使用订阅？

不, 你的信用额度在新帐户中不可用。 接受传输请求的用户需要具有 Visual Studio 许可证, 才能接受传输请求。 订阅使用用户帐户中提供的 Visual Studio 信用额度。 有关详细信息, 请参阅[传输 Visual Studio、Microsoft 合作伙伴网络 (MPN) 和即用即付开发/测试订阅](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)。


## <a name="frequently-asked-questions-faq-for-recipients"></a>收件人常见问题 (FAQ)

这些 Faq 适用于从其他帐户接受 Azure 订阅的帐单所有权的用户。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果使用其他帐户对订阅进行计费, 则该帐户中的用户是否可以继续访问我的资源？

是的。 但是, 如果你的帐户位于与订阅的租户不同的 Azure AD 租户中, 并且发送了传输请求的用户将订阅移到你的帐户租户、任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)删除分配。 若要查看具有[基于角色的访问 (RBAC)](../role-based-access-control/overview.md)访问权限的用户来管理订阅中的资源, 请使用以下步骤:

1. 访问[Azure 门户中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)的 "订阅" 页。
1. 选择要检查的订阅, 然后从左侧窗格中选择 "**访问控制 (IAM)** "。
1. 从页面顶部选择 "**角色分配**"。 "角色分配" 页列出了对订阅拥有 RBAC 访问权限的所有用户。

即使在传输过程中删除了[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)分配, 原所有者帐户中的用户仍可以通过某些安全机制访问订阅, 包括:

* 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
* 存储空间等服务的访问密钥。 有关详细信息，请参阅[有关 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。
* Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其资源的访问, 则应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

  1. 登录到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“所有资源”。
  3. 选择资源。
  4. 在资源页中, 单击 "**设置**"。 可以在这里查看和更新现有机密。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果在计费周期中间接管订阅的计费所有权, 是否需要支付整个计费周期的费用？

帐户负责支付从上传的时间报告的任何使用量。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 使用情况包含在帐户的帐单中。

### <a name="can-i-use-a-different-payment-method"></a>我可以使用不同的付款方式吗？

是的。 在接受传输请求时, 你可以选择链接到你的帐户的现有付款方式或添加新的付款方式。

## <a name="troubleshooting"></a>疑难解答

### <a id="no-button"></a> 为什么我没有看到“转让订阅”按钮？

自助服务订阅转让不适用于你的计费帐户。 目前, 我们不支持在 Azure 门户中转移企业协议 (EA) 帐户中的订阅的计费所有权。 此外, 与 Microsoft 代表一起工作时创建的 Microsoft 客户协议帐户不支持传输计费所有权。 

### <a id="no-button"></a>为什么订阅类型不支持传输？ 

并非所有订阅类型都支持计费所有权转让。 若要查看支持传输的订阅类型的列表, 请参阅[支持的订阅类型](#supported-subscription-types)

### <a id="no-button"></a>为什么在尝试转移订阅的帐单所有权时收到拒绝访问错误？ 

如果你尝试传输 Microsoft Azure 计划订阅, 但没有必要的权限, 则会看到此错误。 若要传输 Microsoft Azure 计划订阅, 你需要是订阅要对其计费的发票部分的所有者或参与者。 有关详细信息, 请参阅[管理发票订阅部分](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
