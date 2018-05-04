---
title: 如何配置非库应用程序的密码单一登录 | Microsoft Docs
description: 自定义的非库应用程序未在 Azure AD 应用程序库中列出时，如何对其配置安全的基于密码的单一登录
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: b928d71fbff232ae93dbf3d24517e7efc4cf8bfd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的密码单一登录

除了 Azure AD 应用程序库中存在的选项外，还可以在所需应用程序未在此处列出时选择添加**非库应用程序**。 使用此功能，可以添加已存在于组织中的任何应用程序或任何你可能使用的来自供应商（该供应商尚不在 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)中）的第三方应用程序。

添加非库应用程序后，可以通过在 [Azure 门户](https://portal.azure.com/)中的“企业应用程序”上选择“单一登录”导航项，来配置此应用程序使用的单一登录方法。

可用的单一登录方法之一是[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)选项。 通过**添加非库应用程序**体验，可以集成任何呈现基于 HTML 的用户名和密码输入字段的应用程序，即使该应用程序不在我们的预集成应用程序集中。

此处的原理是页面抓取技术，该技术是“访问面板”扩展的一部分，允许自动检测用户名和密码输入字段，并为特定应用程序实例安全地存储这些字段。 然后在用户通过应用程序访问面板导航到该应用程序时，安全地向这些字段重播用户名和密码。

这是开始将任何类型的应用程序快速集成到 Azure AD 中的绝佳方式，并且可以：

-   将**世界上的任何应用程序**与 Azure AD 租户集成，前提是该应用程序呈现 HTML 用户名和密码输入字段

-   通过安全地存储和重播已集成到 Azure AD 的应用程序用户名和密码，启用“用户单一登录”

-   为任何应用程序**自动检测输入**字段，并允许在自动检测无法找到的情况下使用访问面板浏览器扩展进行手动检测

-   **支持需要多个登录字段的应用程序**，这适用于不只需要用户名和密码字段才能登录的应用程序

-   **自定义标签**，即指用户输入其凭据时，会在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的用户名和密码输入字段的标签

-   允许**用户**为正在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手动键入的任何现有应用程序帐户提供自己的用户名和密码

-   允许**业务组的成员**使用[自助应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许**管理员**在[将用户分配到应用程序](#_How_to_configure_1)时，使用更新凭据功能指定分配给用户的用户名和密码

-   允许**管理员**在[将组分配到应用程序](#assign-an-application-to-a-group-directly)时，使用更新凭据功能指定组中人员使用的共享用户名和密码

以下部分介绍了如何对使用**添加非库应用程序**体验添加的任何应用程序启用[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)。

## <a name="overview-of-steps-required"></a>所需步骤概述

从 Azure AD 库中配置应用程序时需：

-   [添加非库应用程序](#add-a-non-gallery-application)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

-   [将应用程序分配给用户或组](#assign-the-application-to-a-user-or-a-group)

    -   [直接将用户分配到应用程序](#assign-a-user-to-an-application-directly)

    -   [直接将应用程序分配给组](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>添加非库应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  单击“非库应用程序”。

7.  在“名称”文本框中输入应用程序的名称。 选择“添加”。

稍等片刻，便可看到应用程序的配置窗格。

## <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 确保登录字段在 URL 中可见。

10. 将用户分配到应用程序。

11. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

12. **可选：**对于某些社交媒体应用程序，例如 Twitter 和 Facebook，还提供了用于以选定的频率自动滚动更新应用密码的选项。 若要启用此选项，请在代表某个用户或组输入凭据时选择“我希望 Azure AD 自动管理此用户或组的密码”。 然后选择“滚动更新频率(周)”。

## <a name="assign-a-user-to-an-application-directly"></a>直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  若要打开“添加分配”窗格，请单击“用户和组”列表顶部的“添加”按钮。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：**单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

15. 单击“分配”按钮，将应用程序分配给选定用户。

## <a name="assign-an-application-to-a-group-directly"></a>直接将应用程序分配给组

若要直接将一个或多个组分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  若要打开“添加分配”窗格，请单击“用户和组”列表顶部的“添加”按钮。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个组**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**完整组名**，然后单击复选框以将此组添加到“已选择”列表。

13. 选择完所有组后，单击“选择”按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：**单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选组。

15. 单击“分配”按钮，将应用程序分配给所选组。

在很短一段时间后，所选用户便能够在访问面板中启动这些应用程序。

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](active-directory-application-proxy-sso-using-kcd.md)
