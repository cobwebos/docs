---
title: 如何配置 Azure AD 库应用程序的密码单一登录 | Microsoft Docs
description: 如何将应用程序配置为基于密码的单一登录 (如果该应用程序已在 Azure AD 应用程序库中列出)
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146891"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>为 Azure AD 库应用程序配置密码单一登录

从[Azure Active Directory (Azure AD) 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)添加应用程序时, 可以选择希望用户登录该应用程序的方式。 可以通过在[Azure 门户](https://portal.azure.com/)中的企业应用程序上选择 "**单一登录**", 随时配置此选项。

可用的单一登录 (SSO) 选项之一是[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。 这是开始快速将应用程序集成到 Azure AD 的好方法。 它支持以下功能:

-   安全地存储和重播与集成的应用程序的用户名和密码 Azure AD

-   支持需要多个登录字段的应用程序, 而不仅仅是用户名和密码字段

-   允许你自定义用户在输入凭据时在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的 "用户名" 和 "密码" 字段的标签

-   允许用户为在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手动输入的任何现有应用程序帐户提供自己的用户名和密码

-   允许业务组的成员使用[自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许管理员在[将用户分配到应用程序](#assign-a-user-to-an-application-directly)时, 通过使用 "更新凭据" 功能指定分配给用户的用户名和密码。

-   允许管理员使用 "更新凭据" 功能指定一组用户在[向应用程序分配组](#assign-an-application-to-a-group-directly)时的共享用户名或密码

以下部分介绍如何对已位于[Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)中的应用程序启用[基于密码的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="overview-of-required-steps"></a>必需步骤概述
若要从 Azure AD 库配置应用程序, 需要:

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

-   将应用程序分配给用户或组:

    -   [直接将用户分配到应用程序](#assign-a-user-to-an-application-directly)

    -   [直接将应用程序分配给组](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库中添加应用程序, 请执行以下步骤:

1.  打开[Azure 门户](https://portal.azure.com), 并以 "**全局管理员**" 或 "**共同管理员**" 身份登录。

2.  通过选择左侧菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3.  在搜索框中输入 " **azure active directory** ", 然后选择 " **Azure Active Directory**项。

4.  选择左侧 "Azure AD" 菜单上的 "**企业应用程序**"。

5.  选择 "**企业应用程序**" 窗格右上角的 "**添加**" 按钮。

6.  在 "**从库中添加**" 部分的 "**输入名称**" 框中, 输入应用程序的名称。

7.  选择要为其配置单一登录的应用程序。

8.  添加应用程序之前, 可以在 "**名称**" 框中更改其名称。

9.  选择 "**添加**" 以添加该应用程序。

稍等片刻，便可看到应用程序的配置窗格。

## <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/), 并以 "**全局管理员**" 或 "**共同管理员**" 身份登录。

2. 通过选择左侧菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中输入 " **azure active directory** ", 然后选择 " **Azure Active Directory**项。

4. 从左侧的 "Azure Active Directory" 菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序**"。

6. 选择要为其配置单一登录的应用程序。

7. 加载应用程序后, 在左侧的应用程序菜单中选择 "**单一登录**"。

8. 选择 **"基于密码的登录模式"** 。

9. [将用户分配到应用程序](#assign-a-user-to-an-application-directly)。

10. 你还可以代表用户提供凭据, 方法是选择用户的行, 选择 "**更新凭据**", 然后输入用户名和密码。 否则, 在用户启动应用程序时, 会提示用户输入其凭据。

## <a name="assign-a-user-to-an-application-directly"></a>直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以 "**全局管理员" 身份**登录。

2. 通过选择左侧菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中输入 " **azure active directory** ", 然后选择 " **Azure Active Directory**项。

4. 从左侧的 "Azure Active Directory" 菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序**"。

6. 选择要向其分配用户的应用程序。

7. 加载应用程序后, 在左侧的应用程序菜单中选择 "**用户和组**"。

8. 选择 "**用户和组**" 列表顶部的 "**添加**" 按钮, 以打开 "**添加分配**" 窗格。

9. 从 "**添加分配**" 窗格中选择 "**用户和组**"。

10. 在 "**按名称或电子邮件地址**搜索" 搜索框中输入用户的全名或电子邮件地址。

11. 将鼠标悬停在列表中的用户上, 然后选中用户的个人资料照片或徽标旁边的复选框, 将其添加到 "**选定**" 列表。

12. 可选：如果要添加多个用户, 请在 "**按名称或电子邮件地址搜索**" 框中输入其他全名或电子邮件地址, 并选中该用户的复选框, 以将其添加到 "**选定**" 列表。

13. 选择完用户后, 使用 "**选择**" 按钮将它们添加到要分配给应用程序的用户和组的列表中。

14. 可选：使用 "**添加分配**" 窗格中的 "**选择角色**" 命令选择要分配给所选用户的角色。

15. 选择 "**分配**", 将应用程序分配给选定用户。

## <a name="assign-an-application-to-a-group-directly"></a>直接将应用程序分配给组

若要直接将一个或多个组分配到应用程序，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以 "**全局管理员" 身份**登录。

2. 通过选择左侧菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中输入 " **azure active directory** ", 然后选择 " **Azure Active Directory**项。

4. 从左侧的 "Azure AD" 菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序**"。

6. 选择要向其分配用户的应用程序。

7. 加载应用程序后, 在左侧的应用程序菜单中选择 "**用户和组**"。

8. 选择 "**用户和组**" 列表顶部的 "**添加**" 按钮, 以打开 "**添加分配**" 窗格。

9. 从 "**添加分配**" 窗格中选择 "**用户和组**"。

10. 在 "**按名称或电子邮件地址**搜索" 搜索框中输入要分配的组的完整组名。

11. 将鼠标悬停在列表中的 "**组**上方", 然后选中组的个人资料照片或徽标旁边的复选框, 将该组添加到 "**选定**" 列表。

12. 可选：如果要添加多个组, 请在 "**按名称或电子邮件地址**搜索" 搜索框中输入另一个完整组名, 然后选中相应的复选框以将此组添加到 "**选定**" 列表。

13. 选择完组后, 使用 "**选择**" 按钮将它们添加到要分配给应用程序的用户和组的列表中。

14. 可选：使用 "**添加分配**" 窗格中的 "**选择角色**" 命令选择要分配给所选组的角色。

15. 选择 "**分配**", 将应用程序分配到所选组。

一小段时间后, 所选用户应该能够从访问面板启动这些应用程序。

## <a name="next-steps"></a>后续步骤
[通过应用程序代理提供对应用的单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。
