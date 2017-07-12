---
title: "如何配置 Azure AD 库应用程序的密码单一登录 | Microsoft Docs"
description: "应用程序已在 Azure AD 应用程序库中列出后，如何对其配置安全的基于密码的单一登录"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: d4678afa4d7a9000761d3a60a54539fc1730f76a
ms.contentlocale: zh-cn
ms.lasthandoff: 04/11/2017

---

<a id="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application" class="xliff"></a>

# 如何配置 Azure AD 库应用程序的密码单一登录

从 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)添加应用程序时，可以选择用户登录到该应用程序的方式。 可随时通过在 [Azure 门户](https://portal.azure.com/)中选择企业应用程序上的**单一登录**导航项进行配置。

可用的单一登录方法之一是[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)选项。 这是将应用程序快速集成到 Azure AD 中的绝佳方式，并且可以：

-   通过安全地存储和重播已集成到 Azure AD 的应用程序用户名和密码，启用“用户单一登录”

-   **支持需要多个登录字段的应用程序**，这适用于不只需要用户名和密码字段才能登录的应用程序

-   **自定义标签**，即指用户输入其凭据时，将在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的用户名和密码输入字段的标签

-   允许**用户**为正在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手动键入的任何现有应用程序帐户提供自己的用户名和密码

-   允许**业务组的成员**使用[自助应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许**管理员**在[将用户分配到应用程序](#assign-a-user-to-an-application-directly)时，使用更新凭据功能指定分配给用户的用户名和密码

-   允许**管理员**在[将组分配到应用程序](#assign-an-application-to-a-group-directly)时，使用更新凭据功能指定组中人员使用的共享用户名和密码

下面介绍如何对 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)中已有的应用程序启用[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)。

<a id="overview-of-steps-required" class="xliff"></a>

## 所需步骤概述
从 Azure AD 库中配置应用程序时需：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [将应用程序配置为使用密码单一登录](#configure-the-application-for-password-single-sign-on)

-   [将应用程序分配给用户或组](#assign-the-application-to-a-user-or-a-group)

    -   [直接将用户分配到应用程序](#assign-a-user-to-an-application-directly)

    -   [直接将应用程序分配给组](#assign-an-application-to-a-group-directly)

<a id="add-an-application-from-the-azure-ad-gallery" class="xliff"></a>

## 从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，以全局管理员或共同管理员身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”边栏选项卡上的右上角，单击“添加”按钮

6.  在“从库添加”部分的“输入名称”文本框中，键入应用程序的名称

7.  选择想要配置为单一登录的应用程序

8.  在添加应用程序之前，可以在“名称”文本框中更改其名称。

9.  单击“添加”按钮，添加该应用程序。

在很短一段时间后，便能看到应用程序的配置边栏选项卡。

<a id="configure-the-application-for-password-single-sign-on" class="xliff"></a>

## 将应用程序配置为使用密码单一登录

要为应用程序配置单一登录，请执行以下步骤：

1.  打开[“Azure 门户”](https://portal.azure.com/)，以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序

7.  应用程序加载后，在应用程序的左侧导航菜单中，单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  [将用户分配到应用程序](#assign-a-user-to-an-application-directly)。

10. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，然后代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

<a id="assign-a-user-to-an-application-directly" class="xliff"></a>

## 直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，以**全局管理员**身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  应用程序加载后，在应用程序的左侧导航菜单中，单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的**用户**上以显示**复选框**。 单击用户头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框将此用户添加到“已选择”列表。

13. 选择完所有用户后，单击“选择”按钮将所有已选择的用户添加到要分配给应用程序的用户和组的列表中。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器可选择要分配给所选用户的角色。

15. 单击“分配”按钮，将应用程序分配给选定用户。

<a id="assign-an-application-to-a-group-directly" class="xliff"></a>

## 直接将应用程序分配给组

若要直接将一个或多个组分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，以**全局管理员**身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  应用程序加载后，在应用程序的左侧导航菜单中，单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个组**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**完整组名**，然后单击复选框以将此组添加到“已选择”列表。

13. 选择完所有组后，单击“选择”按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器以选择要分配给所选组的角色。

15. 单击“分配”按钮，将应用程序分配给所选组。

在很短一段时间后，所选用户便能够在访问面板中启动这些应用程序。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[使用应用程序代理提供到应用的单一登录](active-directory-application-proxy-sso-using-kcd.md)

