---
title: "教程：Azure Active Directory 与 Workplace by Facebook 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教程：Azure Active Directory 与 Workplace by Facebook 集成
本教程旨在说明如何将 Workplace by Facebook 与 Azure Active Directory (Azure AD) 集成。

将 Workplace by Facebook 与 Azure AD 集成提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 Workplace by Facebook 
* 可以为已被授予 Workplace by Facebook 访问权限的用户自动预配帐户
* 可以让用户使用其 Azure AD 帐户自动登录到 Workplace by Facebook（单一登录）
* 可以在一个集中位置管理帐户 

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 CS Stars 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用单一登录的 Workplace by Facebook

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>从库添加 Workplace by Facebook
若要配置 Workplace by Facebook 与 Azure AD 的集成，需要从库中将 Facebook 的 Workplace 添加到托管 SaaS 应用列表。

**若要从库中添加 Workplace by Facebook，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入 **Workplace by Facebook**。
7. 在结果窗格中，选择“Workplace by Facebook”，然后单击“完成”以添加该应用程序。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure Active Directory 中的帐户向 Workplace by Facebook 证明自己的身份。

**若要配置 Workplace by Facebook 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中添加 Workplace by Facebook 后，单击“配置单一登录”。
2. 在“配置应用 URL”屏幕中，输入用户将用来登录 Workplace by Facebook 应用程序的 URL。 即 Workplace by Facebook 租户 URL（示例：https://example.facebook.com/）。 完成后，单击“下一步”。
3. 在其他 Web 浏览器窗口中，以管理员身份登录到 Workplace by Facebook 公司站点。
4. 按照以下 URL 处的说明配置 Workplace by Facebook，以将 Azure AD 用作标识提供者：[https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. 完成后，返回到显示 Azure 经典门户的浏览器窗口，单击复选框以确认已完成了此过程，然后依次单击“下一步”和“完成”。


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>自动将用户预配到 Workplace by Facebook
Azure AD 支持将已分配的用户的帐户详细信息自动同步到 Workplace by Facebook。 此自动同步使 Workplace by Facebook 能够在用户首次尝试登录之前获取对用户进行访问授权所需的数据。 在 Azure AD 中撤销访问权限后，它还可以从 Workplace by Facebook 取消预配用户。

可通过在 Azure 经典门户窗口中单击“配置帐户预配”来设置自动预配。

有关如何配置自动预配的更多详细信息，请参阅 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>将用户分配到 Workplace by Facebook
要使已预配的 AAD 用户能够在其访问面板上看到 Workplace by Facebook，必须在 Azure 经典门户中为其分配访问权限。

**将用户分配到 Workplace by Facebook：**

1. 在 Azure 经典门户中的 Workplace by Facebook 起始页上，单击“分配帐户”。
2. 在“显示”菜单中，选择是要将用户还是将组分配到 Workplace by Facebook，然后单击复选标记按钮。
3. 在结果列表中，选择要分配到 Workplace by Facebook 的用户或组。
4. 在页脚中，单击“分配”按钮。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


