---
title: 将 B2B 协作用户添加为信息辅助角色 - Azure Active Directory | Microsoft Docs
description: B2B 协作可让信息工作者和应用所有者将来宾用户添加到 Azure AD 以进行访问 | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: e590500dd622988226c592352b0b86f16d54a9d4
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983057"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>组织中的用户如何邀请来宾用户访问应用

将来宾用户添加到 Azure AD 中的目录后，应用程序所有者可向该来宾用户发送他们想要共享的应用的直接链接。 Azure AD 管理员还可以设置自助服务管理，使应用程序所有者可以管理自己的来宾用户，即使尚未将来宾用户添加到目录。 为应用配置自助服务后，应用程序所有者可以使用访问面板邀请来宾用户访问应用，或将来宾用户添加到有权访问该应用的组中。 自助服务应用管理要求管理员完成一些初始设置下面是设置步骤的摘要（有关更详细的说明，请参阅本页稍后提供的[先决条件](#prerequisites)）：

 - 为租户启用自助服务组管理
 - 创建要分配到应用的组，并将相应用户设为所有者
 - 为应用配置自助服务，并将该组分配到该应用

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>通过访问面板邀请来宾用户访问应用

为应用配置自助服务后，应用程序所有者可以使用自己的访问面板邀请来宾用户访问他们想要共享的应用。 不一定需要事先将来宾用户添加到 Azure AD。 

1. 转到 `https://myapps.microsoft.com`，打开访问面板。
2. 指向该应用，选择省略号 (**...**)，然后选择“管理应用”。
 
   ![访问面板 - 管理应用](media/add-users-iw/access-panel-manage-app.png)
 
3. 在用户列表的顶部，选择 **+**。
   
   ![访问面板 - 添加用户](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. 在“添加成员”搜索框中，键入来宾用户的电子邮件地址。 （可选）包含一条欢迎消息。
   
   ![访问面板 - 邀请](media/add-users-iw/access-panel-invitation.png)
   
5. 选择“添加”，将邀请发送给来宾用户。 发送邀请后，该用户帐户将以来宾的形式自动添加到目录。

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>邀请某人加入有权访问该应用的组
为应用配置自助服务后，应用程序所有者可以邀请来宾用户加入他们管理的、有权访问他们想要共享的应用的组。 目录中不一定要事先存在该来宾用户。 应用程序所有者遵循以下步骤邀请来宾用户加入该组，以便能够访问该应用。

1. 确保你是有权访问想要共享的应用的自助服务组的所有者。
2. 转到 `https://myapps.microsoft.com`，打开访问面板。
3. 选择“组”应用。
   
   ![访问面板 - 组应用](media/add-users-iw/access-panel-groups.png)
   
4. 在“我拥有的组”下，选择有权访问想要共享的应用的组。
   
   ![访问面板 - 我拥有的组](media/add-users-iw/access-panel-groups-i-own.png)
   
5. 在组成员列表的顶部，选择 **+**。
   
   ![访问面板 - 组 - 添加成员](media/add-users-iw/access-panel-groups-add-member.png)
   
6. 在“添加成员”搜索框中，键入来宾用户的电子邮件地址。 （可选）包含一条欢迎消息。
   
   ![访问面板 - 组邀请](media/add-users-iw/access-panel-invitation.png)
   
7. 选择“添加”，以自动向来宾用户发送邀请。 发送邀请后，该用户帐户将以来宾的形式自动添加到目录。


## <a name="prerequisites"></a>先决条件

自助服务应用管理要求全局管理员和 Azure AD 管理员完成一些初始设置。 在设置过程中，可为应用配置自助服务，并将某个组分配到应用程序所有者可以管理的应用。 此外，可将组配置为允许任何人请求成员身份，但需要组所有者的审批。 （详细了解[自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)。） 

> [!NOTE]
> 不能将来宾用户添加到动态组或已与本地 Active Directory 同步的组。

### <a name="enable-self-service-group-management-for-your-tenant"></a>为租户启用自助服务组管理
1. 以全局管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 选择“组”。
4. 在“设置”下选择“常规”。
5. 在“自助服务组管理”下的“所有者可以在访问面板中管理组成员身份请求”的旁边，选择“是”。
6. 选择“保存”。

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>创建要分配到应用的组，并将相应用户设为所有者
1. 以 Azure AD 管理员或全局管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 选择“组”。
4. 选择“新建组”。
5. 在“组类型”下，选择“安全性”。
6. 键入**组名称**和**组说明**。
7. 在“成员身份类型”下，选择“已分配”。
8. 选择“创建”，并关闭“组”页。
9. 在“组 - 所有组”页上，打开该组。 
10. 在“管理”下，选择“所有者” > “添加所有者”。 搜索应管理应用程序访问权限的用户。 选择该用户，然后单击“选择”。

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>为应用配置自助服务，并将该组分配到该应用
1. 以 Azure AD 管理员或全局管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“企业应用程序” > “所有应用程序”。
4. 在应用程序列表中，找到并打开该应用。
5. 在“管理”下，选择“单一登录”，然后配置应用程序的单一登录。 （有关详细信息，请参阅[如何管理企业应用的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)。）
6. 在“管理”下，选择“自助服务”，然后设置自助服务应用访问权限。 （有关详细信息，请参阅[如何使用自助服务应用访问权限](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)。） 
    > [!NOTE]
    > 对于“应将已分配的用户添加到哪个组?”设置，请选择在上一部分创建的组。
7. 在“管理”下，选择“用户和组”，并检查创建的自助服务组是否显示在列表中。
8. 若要将应用添加到组所有者的访问面板，请选择“添加用户” > “用户和组”。 搜索组所有者并选择用户，单击“选择”，然后单击“分配”将该用户添加到应用。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [Azure Active Directory 管理员如何添加 B2B 协作用户？](add-users-administrator.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [Azure AD B2B 协作授权](licensing-guidance.md)
