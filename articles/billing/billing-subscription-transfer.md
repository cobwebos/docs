---
title: 将 Azure 订阅所有权转让给其他帐户 |Microsoft Docs
description: 介绍如何将 Azure 订阅转让给其他用户，以及有关转让过程的一些常见问题 (FAQ)
keywords: 转让 Azure 订阅, Azure 转让订阅, 将 Azure 订阅移至其他帐户, Azure 更改订阅所有者, 将 Azure 订阅转让给其他帐户
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 110e2f611ba8bfc42fe17de6aa4487683db4a414
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>将 Azure 订阅所有权转让给其他帐户

将你的订阅转让给帐户中心内的另一个用户可更改帐户管理员并移交订阅账单所有权。 要将订阅更改为其他产品/服务，请参阅[将 Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。

> [!IMPORTANT]
> 
> 如果将订阅转让给新的 Azure AD 租户，则[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 中的所有角色分配都将从源租户中永久删除，不会迁移到目标租户。

## <a name="transfer-ownership-of-an-azure-subscription"></a>转让 Azure 订阅的所有权

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. 以帐户管理员身份登录 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。若要找出谁是订阅的帐户管理员，请参阅[常见问题解答](#faq)。

1. 选择要转让的订阅。

1. 使用[支持的产品/服务列表](#supported)，检查产品/服务和产品/服务 ID，验证订阅是否符合自助服务转让资格。

   ![在帐户中心验证订阅的产品/服务 ID](./media/billing-subscription-transfer/image0.png)
1. 单击“转让订阅”。

   ![Azure 帐户订阅选项卡](./media/billing-subscription-transfer/image1.png)
1. 指定接收方。

   > [!IMPORTANT]
   > 
   > 如果将订阅转让给新的 Azure AD 租户，则[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 中的所有角色分配都将从源租户中永久删除，不会迁移到目标租户。

   ![“转让订阅”对话框](./media/billing-subscription-transfer/image2.PNG)

1. 接收方会自动收到含有接受链接的电子邮件。

   ![向接收方发送的订阅转让电子邮件](./media/billing-subscription-transfer/image3.png)
1. 接收方单击该链接并遵照说明操作，包括输入他们的付款信息。

   ![第一个订阅转移网页](./media/billing-subscription-transfer/image4.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/image5.png)
1. 成功！ 订阅现已转让。

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>转让企业协议 (EA) 客户的订阅所有权

企业管理员可以转让注册内的订阅的所有权。 若要开始使用，请参阅 EA 门户中的[装让帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>接受订阅所有权后的后续步骤

1. 现在你是帐户管理员。查看和更新服务管理员、共同管理员和其他 RBAC 角色。 若要了解详细信息，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。
1. 更新与此订阅的服务关联的凭据，包括：
   1. 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)
   1. 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)
   1. Azure 虚拟机等服务的远程访问凭据。 
1. 请在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)[更新此订阅的计费警报](billing-set-up-alerts.md)。 
1. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 门户](https://portal.azure.com)中更新合作伙伴 ID。

<a id="supported"></a>

## <a name="whats-supported"></a>支持的操作：

自助服务订阅转让适用于下表中列出的产品/服务或订阅类型。 目前不能转让免费试用版订阅或 [Azure 开放式许可 (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 订阅。 若要暂时避开此问题，请参阅[将资源移动到新的资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。 若要转让其他订阅（例如[赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)或支持计划），请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

| 产品名称                                                                             | 产品编号 |
|----------------------------------------------------------------------------------------|--------------|
| [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\*[通过 EA 门户](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

### <a name="whoisaa"></a> 谁是订阅的帐户管理员？

帐户管理员是注册或购买 Azure 订阅的人员。 他们有权访问[帐户中心](https://account.azure.com/Subscriptions)并执行各种管理任务，例如创建订阅、取消订阅、更改订阅的计费信息，或者更改服务管理员。 如果不确定谁是订阅的帐户管理员，可使用以下步骤查明。

1. 请访问 [Azure 门户中的订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”下的信息。
1. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有内容都会转让吗？ 包括资源组、VM、磁盘和其他正在运行的服务吗？

所有资源（如 VM、磁盘和网站）都会转让给新的所有者。 但是，不会在不同目录之间传递所设置的任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 策略。 此外，[应用注册](../active-directory//develop/active-directory-integrating-applications.md)和其他特定于租户的服务不会一同转让。

### <a id="no-button"></a> 为什么我没有看到“转让订阅”按钮？

很抱歉，你的产品/服务或国家/地区不支持自助服务订阅转让。 若要转移订阅，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>订阅转让会造成任何服务停机吗？

不会影响服务。 转让订阅时，会取消当前帐户管理员名下的订阅，同时在接收方的帐户名下创建一个订阅。 新的订阅与底层 Azure 服务相关联。 订阅 ID 保持不变。

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>如何通过此过程更改订阅的目录？

Azure 订阅在帐户管理员所属的目录中创建。 更改目录时，将订阅转让给目标目录中的用户帐户即可。 当该用户完成接受转让的步骤后，订阅就会自动移至目标目录。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>如果接管另一个组织的订阅帐单所有权，他们可以继续访问原来的资源吗？

如果将订阅转让给另一个租户，与前一租户关联的用户将失去订阅的访问权限。 即使用户不再是服务管理员或共同管理员，他们仍可以通过其他安全机制来访问订阅，包括：

* 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
* 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。
* Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其资源的访问，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

  1. 转到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“所有资源”。
  3. 选择资源。
  4. 在资源边栏选项卡中，单击“设置”。 可以在这里查看和更新现有机密。

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>如果在计费周期中途转让订阅，接收方需要支付整个计费周期的费用吗？

发送方负责支付转让完成之前所报告的任何使用费。 接收方负责支付转让后所报告的使用费。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 这些使用费包含在接收方的帐单中。

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>接收方是否有权访问使用情况和帐单历史记录？

  提供给接收方的信息只有最新帐单的金额（或目前余额，如果订阅是在生成第一份帐单之前转让的话）。 其余的使用与帐单历史记录不会随着订阅一起转移。

### <a name="can-the-offer-be-changed-during-a-transfer"></a>转让期间可以更改优惠吗？

优惠肯定会保持不变。 要更改产品/服务，请参阅[将 Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>可以将订阅转让给另一国家/地区的用户帐户吗？

否，不可以将订阅转让给另一国家/地区的用户帐户。 接收方的用户帐户必须在相同的国家/地区。

### <a name="can-the-recipient-use-a-different-payment-method"></a>接收方可以使用不同的付款方式吗？

是的。 但订阅帐单会拆分成两个帐户。  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>转让 Azure 订阅后，付款方式会受到影响吗？

若要接受订阅转让，必须提供信用卡或类似的付款方式为订阅付款。 例如，如果 Bob 将订阅转让给 Jane，而 Jane 接受转让，那么 Jane 必须提供订阅的付款方式。 转让完成后，将向 Jane（而非 Bob）收取订阅费用。

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何将 Azure 订阅的数据和服务迁移到新的订阅？

如果无法转让订阅所有权，可以手动迁移资源。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。