---
title: "转让 Azure 订阅的所有权 | Microsoft Docs"
description: "如何将 Azure 订阅转让给另一用户，以及有关转让过程的一些常见问题 (FAQ)"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 1faa99b774d5f6cc4e4939ee465809710fd31524
ms.openlocfilehash: 0cb3237f923faa32d77285775dafde55d678c7db


---
# <a name="transferring-ownership-of-an-azure-subscription"></a>转让 Azure 订阅的所有权

在帐户中心中，可将即用即付、Visual Studio、Action Pack 或 BizSpark 订阅转让给其他用户。 还支持转让从 Azure 外部服务的这些订阅类型。 

如果出现以下情况，则可能需要转让 Azure 订阅的所有权：

* 需要将 Azure 订阅的帐单所有权转交给其他人。
* 想要更改用于注册 Azure 的帐户。 以前使用 Microsoft 帐户，但现在想要使用工作或学校帐户。
* 想要将 Azure 订阅从一个目录转移到另一个目录。
* 想要合并位于不同租户中的 Azure 和 Office 365。

若要将订阅更改为其他产品/服务，请参阅[将 Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。 

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>如何转让 Azure 订阅的所有权
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. 登录 <https://account.windowsazure.com/Subscriptions>。 只有帐户管理员才能执行所有权转让。 若要找出谁是订阅的帐户管理员，请参阅[常见问题](#faq)。

2. 选择要转让的订阅。

3. 单击“转让订阅”选项。 如果没有看到按钮，请参阅[常见问题](#no-button)。

   ![Azure 帐户订阅选项卡](./media/billing-subscription-transfer/image1.png)
4. 根据提示指定接收方。

   ![“转让订阅”对话框](./media/billing-subscription-transfer/image2.PNG)
5. 接收方会自动收到含有接受链接的电子邮件。

   ![向接收方发送的订阅转让电子邮件](./media/billing-subscription-transfer/image3.png)
6. 接收方单击该链接并遵照说明操作，包括输入他们的付款信息。

   ![第一个订阅转移网页](./media/billing-subscription-transfer/image4.png)

   ![第二个订阅转移网页](./media/billing-subscription-transfer/image5.png)
7. 成功！ 订阅现已转让。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)
* <a name="whoisaa"></a>**谁是订阅的帐户管理员？**

  帐户管理员是注册或购买 Azure 订阅的人员。 他们有权访问[帐户中心](https://account.windowsazure.com/Home/Index)并执行各种管理任务，例如创建订阅、取消订阅、更改订阅的计费信息，或者更改服务管理员。 如果不确定谁是订阅的帐户管理员，可使用以下步骤查明。

  1. 登录到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“订阅”。
  3. 选择要检查的订阅，然后关注“设置”下的信息。
  4. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

* **所有内容都会转让吗？包括资源组、VM、磁盘和其他正在运行的服务吗？**

  是的，所有资源（如 VM、磁盘和网站）都会转让给新的所有者。 但是，不会转让所设置的任何[管理员角色](billing-add-change-azure-subscription-administrator.md)和[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 政策。 

* <a id="no-button"></a>**为什么我没有看到“转让订阅”按钮？**

  如果没有看到“转让订阅”按钮，表示不支持转让该产品/服务的订阅。 请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

* **订阅转让会造成任何服务停机吗？**

  不会影响服务。 转让订阅时，会取消当前帐户管理员名下的订阅，同时在接收方的帐户名下创建一个订阅。 新的订阅与底层 Azure 服务相关联。 订阅 ID 保持不变。

* **如何通过此过程更改订阅的目录？**

  Azure 订阅在帐户管理员所属的目录中创建。 更改目录时，将订阅转让给目标目录中的用户帐户即可。 当该用户完成接受转让的步骤后，订阅就会自动移至目标目录。

* **如果接管另一个组织的订阅帐单所有权，他们可以继续访问原来的资源吗？**

  如果将订阅转让给另一个租户，与前一租户关联的用户将失去订阅的访问权限。 即使用户不再是服务管理员或共同管理员，他们仍可以通过其他安全机制来访问订阅，包括：

  * 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
  * 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。
  * Azure 虚拟机等服务的远程访问凭据。

  此列表并不完整。 如果接收方需要限制对其资源的访问，则应考虑更新与服务关联的任何机密。 可按如下所述更新大多数资源：

    1. 转到 [Azure 门户](https://portal.azure.com)。
    2. 在“中心”菜单上，选择“所有资源”。
    3. 选择资源。 
    4. 在资源边栏选项卡中，单击“设置”。 你可以在这里查看和更新现有机密。

* **如果在计费周期中途转让订阅，接收方需要支付整个计费周期的费用吗？**

  发送方负责支付转让完成之前所报告的任何使用费。 接收方负责支付转让后所报告的使用费。 可能有些使用费是在转让之前发生的，但在转让之后才报告。 这些使用费包含在接收方的帐单中。

* **接收方是否有权访问使用情况与帐单？**

  提供给接收方的信息只有最新帐单的金额（或目前余额，如果订阅是在生成第一份帐单之前转让的话）。 其余的使用与帐单历史记录不会随着订阅一起转移。

* **转让期间可以更改优惠吗？**

  优惠肯定会保持不变。 若要更改产品/服务，请参阅[将 Azure 订阅切换到其他产品/服务](billing-how-to-switch-azure-offer.md)。

* **可以将订阅转让给另一国家/地区的用户帐户吗？**

  否，不可以将订阅转让给另一国家/地区的用户帐户。 接收方的用户帐户必须在相同的国家/地区。

* **接收方可以使用不同的付款方式吗？**

  是的。 但订阅帐单会拆分成两个帐户。  

* **转让 Azure 订阅后，付款方式会受到影响吗？**

  若要接受订阅转让，必须提供信用卡或类似的付款方式为订阅付款。 例如，如果 Bob 将订阅转让给 Jane，而 Jane 接受转让，那么，Jane 还必须提供订阅付款方式。 完成转让后，就不会再为转让给 Jane 的订阅向 Bob 收费。

* **如何将 Azure 订阅的数据和服务迁移到新的订阅？**

  如果无法转让订阅所有权，则可以手动迁移资源。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>转让企业协议 (EA) 客户的订阅所有权
企业管理员可以转让注册内的订阅的所有权。 若要开始使用，请参阅 EA 门户中的[装让帐户所有权](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>接受订阅所有权后的后续步骤
1. 现在你是帐户管理员。 请查看并更新服务管理员和共同管理员。 在 [Azure 经典门户](https://manage.windowsazure.com)中转到“设置”来管理管理员。 [了解有关管理员角色的详细信息](billing-add-change-azure-subscription-administrator.md)。

2. 用户还可以针对订阅和服务使用基于角色的访问控制 (RBAC)。 访问 [Azure 门户](https://portal.azure.com)。 [了解有关 RBAC 的详细信息](../active-directory/role-based-access-control-configure.md)

3. 更新与此订阅服务关联的凭据。 其中包括：
   
   * 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上载 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)
   
   * 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)
   
   * Azure 虚拟机等服务的远程访问凭据。 

4. 请在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)[更新此订阅的计费警报](billing-set-up-alerts.md)。 

5. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)更新合作伙伴 ID。


## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。 





<!--HONumber=Feb17_HO2-->


