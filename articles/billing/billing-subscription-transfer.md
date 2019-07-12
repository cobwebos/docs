---
title: Azure 订阅的记帐所有权转让给其他帐户 |Microsoft Docs
description: 介绍如何将 Azure 订阅的计费所有权转让给另一个帐户，以及一些常见问题 (FAQ) 有关过程
keywords: 传输 azure 订阅，azure 转让订阅，将 azure 订阅移到另一个帐户，azure 更改订阅所有者，将 azure 订阅转让给另一个帐户，azure 传输计费
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
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657869"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure 订阅的记帐所有权转让给其他帐户

你可能想要你的 Azure 订阅的记帐所有权转让如果离开你的组织，或者你想要对另一个帐户收费的订阅。 将计费所有权转让给另一个帐户提供新的帐户权限，以执行以下计费任务中的管理员更改支付方式、 查看费用，和取消订阅。

如果你想要保留但更改的计费所有权的你的订阅类型，请参阅[将 Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。 如果想要控制哪些人可以管理订阅中的资源，请参阅[Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果你是企业协议 （ea） 客户，企业管理员可以订阅的记帐所有权转让帐户之间。 有关详细信息，请参阅[企业协议 (EA) 订阅的记帐所有权转让](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure 订阅的记帐所有权转让

1. 登录到[Azure 门户](https://portal.azure.com)作为具有你想要传输的订阅的计费帐户的管理员。 若要了解是否你是管理员，请参阅[方面的常见问题](#faq)。

1. 在“成本管理 + 计费”  中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 选择**订阅**从左侧窗格。 根据您的访问权限，可能需要选择计费的作用域，然后选择**订阅**或**Azure 订阅**。

1. 选择**转移计费所有权**你想要传输的订阅。 

   ![选择要传输的订阅](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 输入将是新所有者的订阅，然后选择的帐户的计费管理员的用户的电子邮件地址**发送传输请求**。

    > [!IMPORTANT]
    >
    > 如果给用户的帐户订阅的记帐所有权转让中另一个 Azure AD 租户，所有[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)分配来管理订阅中的资源将被永久删除。 只有新所有者将有权管理订阅中的资源。 有关详细信息，请参阅[将订阅传输到另一个 Azure AD 租户中的用户](../active-directory/managed-identities-azure-resources/known-issues.md)。
  
    ![发送传输页](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 用户会收到一封电子邮件的说明以查看你传输的请求。

   ![向接收方发送的订阅转让电子邮件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要批准该传输请求，用户的电子邮件中选择的链接并按照的说明。 用户需要选择将用于为订阅付费的付款方法。 此外，如果用户没有 Azure 帐户，他们将需要注册新帐户。 

   ![第一个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 订阅现已转让。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>将订阅传输到另一个 Azure AD 租户中的帐户

当你注册 Azure 时，将为您创建一个 Azure Active Directory (AD) 租户。 租户代表你的帐户。 使用租户来管理对你的订阅和资源的访问。

当创建新的订阅时，它位于你的帐户的 Azure AD 租户。 如果你想要提供给你的订阅或资源的其他人访问，则需要邀请到你的租户。 这有助于你控制对你的订阅和资源的访问。

当你的订阅的记帐所有权转让给另一个 Azure AD 租户中的帐户时，订阅移至新帐户的租户。 所有用户、 组或服务主体有[基于角色的访问 (RBAC)](../role-based-access-control/overview.md)来管理订阅中的资源失去访问权限。 只有在新接受转移请求的帐户中的用户将具有访问权限管理的资源。 若要提供给最初具有访问权限的用户访问，新的所有者必须将加入[手动将这些用户添加到订阅](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>传输 Visual Studio、 Microsoft 合作伙伴网络 (MPN) 和即付开发/测试订阅

Visual Studio 和 Microsoft 合作伙伴网络订阅具有与其关联的每月重复执行的 Azure 额度。 如果您的传输这些订阅，你的信用额度不可用目标计费帐户中。 订阅目标计费帐户中使用的信用额度。 例如，如果 Bob 将 Visual Studio Enterprise 订阅转让给 Jane 的第九年 9 月上的帐户和 Jane 接受转让。 传输完成后，订阅开始使用 Jane 的帐户中的信用额度。 信用额度将重置每月的第 9 个。 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>企业协议 (EA) 订阅的记帐所有权转让

企业管理员可以在注册内帐户间传输订阅的所有权。 有关详细信息，请参阅[帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)在 EA 门户中。

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>接受计费所有权后的后续步骤

如果已接受的 Azure 订阅计费所有权，我们建议您查看这些后续步骤：

1. 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
1. 更新与此订阅的服务关联的凭据，包括：
   1. 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)
   1. 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)
   1. Azure 虚拟机等服务的远程访问凭据。
1. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 门户](https://portal.azure.com)中更新合作伙伴 ID。 有关详细信息，请参阅[将合作伙伴 ID 链接到 Azure 帐户](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支持的订阅类型

下面列出的订阅类型可以在 Azure 门户中的订阅转移。 有关目前不支持传输[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)或[Azure 中打开许可 (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/)订阅。 若要暂时避开此问题，请参阅[将资源移动到新的资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。 若要转让其他订阅，例如[赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)支持计划，或[联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

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
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [通过 EA 门户](#EA)。

\*\* 仅支持创建的帐户在登录过程会在 Azure 网站上。 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>方面的常见问题 (FAQ) 的发件人

这些常见问题适用于用户，会将 Azure 订阅计费所有权转让给另一个帐户。

### <a name="whoisaa"></a> 谁是帐户的计费管理员？

计费管理员是有权管理的帐户的帐单的人。 已授权他们访问的计费[Azure 门户](https://portal.azure.com)并执行各种计费任务，例如创建订阅、 视图和即付发票或更新付款方式。

若要识别您为其是计费管理员的订阅，请使用以下步骤：

1. 请访问[成本管理 + Azure 门户中的计费页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 选择**订阅**从左侧窗格。 根据您的访问权限，可能需要选择计费的作用域，然后选择**订阅**或**Azure 订阅**
1. 订阅页列出所有订阅的为计费管理员。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有内容都会转让吗？ 包括资源组、VM、磁盘和其他正在运行的服务吗？

所有资源都如 Vm、 磁盘和网站传输到新帐户。 但是，如果您将订阅转让给帐户在另一个 Azure AD 租户，任何[管理员角色](billing-add-change-azure-subscription-administrator.md)并[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)分配的订阅[不这样做传输](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)。 此外，[应用注册](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他特定于租户的服务无法与订阅一起转移。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>可以将所有权转移到另一个国家/地区中的帐户？
遗憾的是，跨国家/地区传输无法执行在 Azure 门户中。 若要跨国家/地区，转移你的订阅[请联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是两个帐户上的管理员。 可以将订阅转让从一个我帐户到另一个？
是的可以将订阅转让你的帐户之间。 你的帐户从概念上讲被视为两个不同用户帐户，以便可以使用上面的步骤将你的帐户之间传输订阅。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>订阅转让会造成任何服务停机吗？

如果将订阅转让给相同的 Azure AD 租户中的用户时，则不会影响到运行在订阅中的资源。  但是，如果将订阅转让给另一个中的用户租户，所有用户、 组和服务主体有[基于角色的访问 (RBAC)](../role-based-access-control/overview.md)来管理订阅中的资源失去访问权限。 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>接收方是否有权访问使用情况和帐单历史记录？

提供给接收方的唯一信息是为你的订阅上个月的成本。 其余的使用情况和帐单历史记录不会传输与订阅

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何将 Azure 订阅的数据和服务迁移到新的订阅？

如果无法转让订阅所有权，可以手动迁移资源。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果将 Visual Studio 或 Microsoft 合作伙伴网络订阅转让，没有我的信用会包含在新的帐户中的订阅？

不可以，你的信用额度不适用于新帐户。 接受转移请求的用户需要具有 Visual Studio 许可证，以接受该传输请求。 订阅使用的是用户的帐户中可用的 Visual Studio 信用额度。 有关详细信息，请参阅[传输 Visual Studio、 Microsoft 合作伙伴网络 (MPN) 和即付开发/测试订阅](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>方面的常见问题 (FAQ) 的接收者

这些常见问题将应用于接管从另一个帐户的 Azure 订阅的计费所有权的用户。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果接管另一个帐户的订阅帐单所有权，做用户，帐户继续有权访问我的资源？

如果将订阅转让到相同的 Azure AD 租户，所有用户、 组和服务主体有中的帐户[基于角色的访问 (RBAC)](../role-based-access-control/overview.md)来管理订阅中的资源保留他们的访问权限。 若要查看具有对订阅的 RBAC 访问权限的用户，请使用以下步骤：

1. 请访问[在 Azure 门户中的订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择你想要检查，然后选择的订阅**访问控制 (IAM)** 从左侧窗格。
1. 选择**角色分配**从页面顶部。 角色分配页列出了所有用户都具有对订阅的 RBAC 访问权限。

如果将订阅转让到另一个 Azure AD 租户，所有用户、 组和服务主体有中的帐户[基于角色的访问 (RBAC)](../role-based-access-control/overview.md)来管理订阅中的资源失去访问权限。 但是，即使它们不不再拥有 RBAC 访问权限，他们仍可以通过某些安全机制，包括订阅的访问权限：

* 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
* 存储空间等服务的访问密钥。 有关详细信息，请参阅[有关 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。
* Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制、 其资源的访问权限，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

  1. 登录到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“所有资源”  。
  3. 选择资源。
  4. 在资源页上，单击**设置**。 可以在这里查看和更新现有机密。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果接管在计费周期中途订阅帐单所有权，是否需要支付整个计费周期内？

你的帐户是负责对从传输及更高版本的时间报告任何使用情况付款。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 这些使用费包含在你的帐户的帐单。

### <a name="can-i-use-a-different-payment-method"></a>可以使用不同的付款方式吗？

是的。 时接受转移请求，可以选择现有的链接到你的帐户或添加新的付款方式付款方法。

## <a name="troubleshooting"></a>疑难解答

### <a id="no-button"></a> 为什么我没有看到“转让订阅”按钮？

遗憾的是，自助服务订阅转让不适用于您的计费帐户。 目前，我们不支持在 Azure 门户中的企业协议 (EA) 帐户的计费所有权传输。 此外，通过 Microsoft 销售工作时创建的 Microsoft 客户协议帐户不支持将订阅计费所有权转让。 

### <a id="no-button"></a> 为什么我的订阅键入支持传输？ 

遗憾的是，并非所有类型的订阅都支持计费所有权的转移。 若要查看支持传输的订阅类型的列表，请参阅[支持的订阅类型](#supported-subscription-types)

### <a id="no-button"></a> 为什么会收到拒绝访问错误尝试订阅的记帐所有权转让时？ 

如果想要将 Microsoft Azure 计划的订阅转让，但没有所需的权限，您将看到此错误。 若要传输 Microsoft Azure 的计划订阅，您需要是所有者或参与者向其计费的订阅的发票部分。 有关详细信息，请参阅[管理订阅的发票部分](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 查看和更新服务管理员、共同管理员和其他 RBAC 角色。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
