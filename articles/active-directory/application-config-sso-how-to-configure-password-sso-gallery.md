---
title: 如何配置 Azure AD 库应用程序的密码单一登录 | Microsoft Docs
description: 应用程序已在 Azure AD 应用程序库中列出后，如何对其配置安全的基于密码的单一登录
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 22a7a4e25a3df1e9f50b9b3038d4a6bd90ecd408
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335068"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的密码单一登录

从 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)添加应用程序时，可以选择用户登录到该应用程序的方式。 可随时通过在 [Azure 门户](https://portal.azure.com/)中选择企业应用程序上的**单一登录**导航项进行配置。

可用的单一登录方法之一是[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)选项。 这是将应用程序快速集成到 Azure AD 中的绝佳方式，并且可以：

-   通过安全地存储和重播已集成到 Azure AD 的应用程序用户名和密码，启用“用户单一登录”

-   **支持需要多个登录字段的应用程序**，这适用于不只需要用户名和密码字段才能登录的应用程序

-   **自定义标签**，即指用户输入其凭据时，会在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的用户名和密码输入字段的标签

-   允许**用户**为正在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手动键入的任何现有应用程序帐户提供自己的用户名和密码

-   允许**业务组的成员**使用[自助应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许**管理员**在[将用户分配到应用程序](#assign-a-user-to-an-application-directly)时，使用更新凭据功能指定分配给用户的用户名和密码

-   允许**管理员**在[将组分配到应用程序](#assign-an-application-to-a-group-directly)时，使用更新凭据功能指定组中人员使用的共享用户名和密码

以下部分介绍了如何对 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)中已有的应用程序启用[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)。

## <a name="overview-of-steps-required"></a>所需步骤概述
若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

-   [将应用程序分配给用户或组](#assign-the-application-to-a-user-or-a-group)

    -   [直接将用户分配到应用程序](#assign-a-user-to-an-application-directly)

    -   [直接将应用程序分配给组](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  单击“添加”按钮，添加该应用程序。

稍等片刻，便可看到应用程序的配置窗格。

## <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  [将用户分配到应用程序](#assign-a-user-to-an-application-directly)。

10. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

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

8.  单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：** 如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

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

8.  单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”列表。

12. **可选：** 如果想要**添加多个组**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**完整组名**，然后单击复选框以将此组添加到“已选择”列表。

13. 选择完所有组后，单击“选择”按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选组。

15. 单击“分配”按钮，将应用程序分配给所选组。

在很短一段时间后，所选用户便能够在访问面板中启动这些应用程序。

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
