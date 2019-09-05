---
title: 从云合作伙伴门户到合作伙伴中心的帐户迁移-适用于 Azure 的商业 Marketplace
description: 如何将帐户从 CPP 迁移到合作伙伴中心。 -适用于 Azure 的商业 Marketplace
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383292"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>从云合作伙伴门户到合作伙伴中心的帐户迁移

当你的产品/服务从云合作伙伴门户（CPP）迁移到合作伙伴中心（PC）时，它们将被锁定，以便在 CPP 中进行编辑。 此时，你的帐户设置需要迁移到合作伙伴中心。 你的帐户设置以及你的产品/服务都可以在合作伙伴中心进行管理。

## <a name="account-migration-process"></a>帐户迁移过程

当提供程序从 CPP 迁移时，将为你的帐户配置迁移。 
 
如果用户是具有给定帐户的 CPP 中具有 "所有者" 角色的用户，则发布者配置文件页上会显示黄色横幅。 系统会要求将帐户设置移动到合作伙伴中心。 

单击横幅以启动帐户迁移过程。 应输入以下项：

### <a name="work-email-address"></a>工作电子邮件地址

在大多数情况下，使用登录到 CPP 时所用的电子邮件地址登录。 在某些情况下，必须使用不同的电子邮件地址：

* Microsoft 帐户：如果 CPP 帐户是 Microsoft 帐户，则需要输入与租户关联的有效工作电子邮件地址，该地址是为其注册 MPN ID 的。

* 租户不匹配：如果工作电子邮件地址不属于与你的 CPP 帐户上存在的 Microsoft 合作伙伴网络 ID 相关联的租户，则会看到错误。 若要移动此错误，请输入与租户关联的电子邮件地址。 错误消息将提供租户的名称。

### <a name="sign-up-for-microsoft-partner-network-program"></a>注册 Microsoft 合作伙伴网络程序

如果你的 CPP 帐户没有 Microsoft 合作伙伴网络 ID，或具有无效的 ID，则在激活过程中，你将需要注册 Microsoft 合作伙伴网络计划。

## <a name="after-account-migration-is-complete"></a>帐户迁移完成后

对于给定帐户，迁移只需发生一次。 一旦某个给定合作伙伴完成了帐户的迁移，所有所有者都将在其发布者配置文件页上看到此行为：

1. 你将在 Microsoft 合作伙伴网络中看到 "合作伙伴设置" 页，你现在可以在其中管理帐户设置。 
2. 如果用户拥有 "所有者" 角色，则该页面上的发布者个人资料页面上会显示一个黄色横幅，要求他们管理合作伙伴中心的帐户设置。
3. CPP 中的 "帐户设置" 页被转换为只读模式。

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>将基于 Dynamics 365 的解决方案移动到合作伙伴中心

如果已为客户参与或 Dynamics 365 创建了 Dynamics 365，以便在一个商业伙伴操作门户中为财务和运营解决方案创建了 Dynamics，**现在应在合作伙伴中心管理这些解决方案**。

**如果你未在2019年8月31日之前移动解决方案**，请尽快完成以下步骤。 在此之前：
- Isv 无法访问市场营销权益
- 共同销售优先顺序将失去其状态
- 在2019年10月15日之后，要求云嵌入的那些要求不符合要求

> [!NOTE]
> 如果你的 MPN 成员资格帐户最初是在合作伙伴成员中心（PMC）中创建的，请登录到[合作伙伴中心](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)，以确认你的帐户已迁移，然后才能完成以下步骤。 如果你看到带有 MPN ID 的配置文件屏幕，则已准备好继续。 如果不是，则必须按照[合作伙伴成员中心](https://partners.microsoft.com/partnerprogram/Welcome.aspx)中的提示启动帐户迁移。 如果需要帮助，请访问[支持](https://partner.microsoft.com/support?issueid=100-0077)。

1. 请参阅[合作伙伴中心的 "商用 Marketplace 概述" 页](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。 如果在左侧导航窗格中看到 "商用 Marketplace"，则已注册并应继续下一步。 如果没有，请立即[注册商业市场](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)。
2. [搜索你的产品/服务](https://appsource.microsoft.com/)，以确认你的产品/服务是否在 AppSource 中。 如果产品/服务已 AppSource，则继续下一步。 对于不在 AppSource 中的任何产品/服务，请创建[新的 dynamics 365 For Customer Engagement 产品](create-new-customer-engagement-offer.md)/服务，或使用[新的 Dynamics 365 进行运营](create-new-operations-offer.md)。
3. 在 Business Applications ISV Connect Program 中验证你的注册：
  
   * 在合作伙伴中心的 "[协议](https://partner.microsoft.com/dashboard/account/agreements)" 页上，确保已接受**Business Applications ISV 附录**来注册该计划。
   * 在 "[帐户设置](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)" 页上，提供你的计费信息。

4. 提交用于认证的每个新的和现有的产品/服务，即使您的产品/服务先前已经过认证。 如果有资格，你可以在此过程中请求参与高级层。 如果你的产品/服务已**通过认证，则必须完成应用重新认证，2019年10月15日。** 认证或重新认证将要求你的应用支持最新版本的 Business Applications 平台。
5. 请在 "Marketplace 链接" 部分中转到[一个商业合作伙伴](https://msgtm.azurewebsites.net/en-US/Profile/SignIn)直接连接门户并添加 APPSOURCE 列表 URL。 如果需要此步骤的帮助，请向我们cosell@microsoft.com发送电子邮件。

## <a name="next-steps"></a>后续步骤

- [在合作伙伴中心管理你的商业应用商店帐户](./manage-account.md) 
