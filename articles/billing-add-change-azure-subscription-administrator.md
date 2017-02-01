---
title: "如何添加或更改 Azure 管理员角色 | Microsoft Docs"
description: "描述如何添加或更改 Azure 共同管理员、服务管理员和帐户管理员"
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9083872545675cb700f4cfae675331484aed933b
ms.openlocfilehash: 9a3c67d88fc1da6582e07d948cf5be5a2d6db3d2


---
# <a name="how-to-add-or-change-azure-administrator-roles"></a>如何添加或更改 Azure 管理员角色
 帐户管理员、服务管理员和共同管理员是 Microsoft Azure 中的三种管理员角色。 若要查看计费信息及管理订阅，必须以帐户管理员身份登录到[帐户中心](https://account.windowsazure.com/Home/Index)。 下表描述这三个管理角色之间的差异。

| 管理角色 | 限制 | 说明 |
| --- | --- | --- |
| 帐户管理员 (AA) |每个 Azure 帐户 1 个帐户管理员 |这是注册或购买了 Azure 订阅的人员，有权访问[帐户中心](https://account.windowsazure.com/Home/Index)并执行各种管理任务。 其中包括能够创建订阅、取消订阅、更改订阅的帐单，以及更改服务管理员。 |
| 服务管理员 (SA) |每个 Azure 订阅 1 个服务管理员 |此角色有权管理 [Azure 门户](https://portal.azure.com)中的服务。 默认情况下，新订阅的帐户管理员也是服务管理员。 |
| [Azure 经典门户](https://manage.windowsazure.com)中的共同管理员 (CA) |每个订阅 200 个共同管理员 |此角色具有与服务管理员一样的访问特权，但不能更改订阅与 Azure 目录之间的关联关系。 |

可以使用 Azure Active Directory 基于角色的访问控制 (RBAC) 将用户添加到多个角色。 有关更多信息，请参阅 [Azure Active Directory 基于角色的访问控制](active-directory/role-based-access-control-configure.md)。


## <a name="how-to-add-an-admin-for-a-subscription"></a>如何添加订阅的管理员
**Azure 门户**

在 Azure 门户中将某人添加为订阅的管理员，即向其授予所有者角色。 所有者角色只能管理你分配的订阅中的资源。 此角色没有对其他订阅的访问特权。 通过 [Azure 门户](https://portal.azure.com)添加的所有者不能在 [Azure 经典门户](https://manage.windowsazure.com)中管理资源。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，选择**“订阅”** > *“希望管理员访问的订阅”*。

    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. 在“订阅”边栏选项卡中，选择“访问控制(IAM)”> “添加”。

    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. 选择“选择角色” > “所有者”。

    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. 键入要添加为所有者的用户的电子邮件地址，单击该用户，然后单击“选择”。

    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Azure 经典门户**

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
2. 在导航窗格中，选择**“设置”**> **“管理员”**> **“添加”**。 </br>

    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 键入要添加为共同管理员的人员的电子邮件地址，然后选择允许共同管理员访问的订阅。</br>

    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

下面的电子邮件地址可以添加为共同管理员：

* **Microsoft 帐户**（以前称为 Windows Live ID） </br>
  可以使用 Microsoft 帐户登录所有面向使用者的 Microsoft 产品和云服务，例如 Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone 和 Xbox LIVE。
* **组织帐户**</br>
  组织帐户是指在 Azure Active Directory 下创建的帐户。 组织帐户地址的格式如下：
  
    user@&lt;your domain&gt;.onmicrosoft.com

## <a name="limitations-and-restrictions-to-administrator-accounts"></a>管理员帐户的限制和局限
* 每个订阅都与一个 Azure AD 目录（也称默认目录）相关联。 若要查找与订阅相关联的默认目录，请转到 [Azure 经典门户](https://manage.windowsazure.com/)，然后选择**“设置”** > **“订阅”**。 选中订阅 ID 即可查找默认目录。
* 如果使用 Microsoft 帐户登录，则只能将默认目录中的其他 Microsoft 帐户或用户添加为共同管理员。
* 如果使用组织帐户登录，则可将组织中的其他组织帐户添加为共同管理员。 例如，abby@contoso.com 可以添加 bob@contoso.com 作为服务管理员或共同管理员，但不能添加 john@notcontoso.com，除非 john@noncontoso.com 是默认目录中的用户。 使用组织帐户登录的用户可以继续将 Microsoft 帐户用户添加为服务管理员或共同管理员。
* 既可使用组织帐户登录 Azure，请注意对服务管理员和共同管理员帐户要求方面的下述更改：

  | 登录方法 | 将 Microsoft 帐户或默认目录中的用户添加为 CA 或 SA？ | 将同一组织中的组织帐户添加为 CA 或 SA？ | 将另一组织中的组织帐户添加为 CA 或 SA？ |
  | --- | --- | --- | --- |
  |  Microsoft 帐户 |是 |否 |否 |
  |  组织帐户 |是 |是 |否 |

## <a name="how-to-change-service-administrator-for-a-subscription"></a>如何更改订阅的服务管理员
只有帐户管理员可以更改订阅的服务管理员。

1. 使用帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)。
2. 选择想要更改的订阅。
3. 在右侧，单击“编辑订阅详细信息”。 </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。 </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="how-to-change-the-account-administrator"></a>如何更改帐户管理员
若要将 Azure 帐户的所有权转让给另一个帐户，请参阅[转让 Azure 订阅的所有权](billing-subscription-transfer.md)。

## <a name="how-to-check-the-account-administrator-of-the-subscription"></a>如何查看订阅的帐户管理员
如果不确定谁是订阅的帐户管理员，可使用以下步骤查明。

  1. 登录到 [Azure 门户](https://portal.azure.com)。
  2. 在“中心”菜单上，选择“订阅”。
  3. 选择要检查的订阅，然后关注“设置”下的信息。
  4. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>了解有关资源访问控制和 Active Directory 的详细信息
* 若要深入了解如何在 Microsoft Azure 中控制资源访问，请参阅[了解 Azure 中的资源访问](active-directory/active-directory-understanding-resource-access.md)。
* 有关 Azure Active Directory 与 Azure 订阅关联方式的信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](active-directory/active-directory-how-subscriptions-associated-directory.md)以及[在 Azure Active Directory 中分配管理员角色](active-directory/active-directory-assign-admin-roles.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。 




<!--HONumber=Dec16_HO2-->


