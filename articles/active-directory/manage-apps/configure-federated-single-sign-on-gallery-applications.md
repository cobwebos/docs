---
title: 如何配置 Azure AD 库应用程序的联合单一登录 | Microsoft Docs
description: 如何为现有 Azure AD 库应用程序配置联合单一登录, 以及如何使用教程快速入门
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
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207131"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的联合单一登录

对于 Azure Active Directory (Azure AD) 库中具有企业单一登录功能的所有应用程序, 都提供了分步教程。 可以访问[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，获取详细的分步指南。

## <a name="overview-of-steps-required"></a>所需步骤概述
若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [在 Azure AD 中配置应用程序的元数据值 (登录 URL、标识符、答复 URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值 (登录 URL、颁发者、注销 URL 和证书)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [将用户分配到应用程序](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录。

2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3.  在搜索框中键入 "Azure Active Directory", 然后选择 " **Azure Active Directory**"。

4.  从 Azure AD 左侧导航菜单中选择 "**企业应用程序**"。

5.  选择 "**企业应用程序**" 窗格右上角的 "**添加**"。

6.  在 "**从库中添加**" 部分的 "**输入名称**" 框中, 键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  添加应用程序前, 可以在 "**名称**" 框中更改其名称。

9.  选择 "**添加**" 以添加该应用程序。

一小段时间后, 您应该可以看到应用程序的配置窗格。

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>为 Azure AD 库中的应用程序配置单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中键入 "Azure Active Directory", 然后选择 " **Azure Active Directory**"。

4. 从 Azure Active Directory 左侧导航菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   * 如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序"。**

6. 选择要配置单一登录的应用程序。

7. 在应用程序加载后, 从应用程序的左侧导航菜单中选择**单一登录**。

8. 从“模式”下拉列表中选择“基于 SAML 的登录”。

9. 在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

   1. 要将应用程序配置为 SP 启动的 SSO，则“登录 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

   2. 要将应用程序配置为 IdP 启动的 SSO，“回复 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

10. 可选：如果要查看非必需值, 请选择 "**显示高级 URL 设置**"。

11. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

12. 可选：选择 "**查看和编辑所有其他用户属性**", 以编辑在用户登录时要发送到 SAML 令牌中的应用程序的属性。

    若要添加属性：
   
    1. 选择“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

    1. 选择“保存”。 然后就能在表中看到新属性了。

13. 选择 **" &lt;配置应用&gt;程序名称**" 以访问有关如何在应用程序中配置单一登录的文档。 此外, 还提供了必需的元数据 Url 和证书, 用于设置应用程序的 SSO。

14. 选择“保存”以保存配置。

15. 将用户分配到应用程序。

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

若要选择用户标识符或添加用户属性, 请执行以下步骤:

1. 打开[Azure 门户](https://portal.azure.com/), 并以 "**全局管理员**" 或 "**共同管理员**" 身份登录。

2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中键入 "Azure Active Directory", 然后选择 " **Azure Active Directory**"。

4. 从 Azure Active Directory 左侧导航菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   * 如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序"。**

6. 选择已配置单一登录的应用程序。

7. 在应用程序加载后, 从应用程序的左侧导航菜单中选择**单一登录**。

8. 在“用户属性”部分的“用户标识符”下拉列表中，为用户选择唯一标识符。 所选项需与应用程序中的期望值匹配，以便对用户进行身份验证。

   >[!NOTE] 
   >Azure AD 根据所选值或 SAML AuthRequest 中应用程序所请求的格式, 为 NameID 属性 (用户标识符) 选择格式。 有关详细信息, 请参阅 NameIDPolicy 部分中的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)。
   >
   >

9. 若要添加用户属性, 请选择 "**查看和编辑所有其他用户属性**", 以编辑在用户登录时要发送到 SAML 令牌中的应用程序的属性。

   若要添加属性：
  
   1. 选择“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 选择**保存**。 然后就能在表中看到新属性了。

## <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

若要从 Azure AD 下载应用程序元数据或证书，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/), 并以 "**全局管理员**" 或 "**共同管理员**" 身份登录。

2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中键入 "Azure Active Directory", 然后选择 " **Azure Active Directory**"。

4. 从 Azure Active Directory 左侧导航菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   *  如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序**"。

6. 选择已配置单一登录的应用程序。

7. 在应用程序加载后, 从应用程序的左侧导航菜单中选择**单一登录**。

8. 中转到 " **SAML 签名证书**" 部分, 然后选择 "**下载**列值"。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

Azure AD 还提供了用于访问元数据的 URL。 使用以下模型获取特定于该应用程序的元数据 URL:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以 "**全局管理员" 身份登录。**

2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。

3. 在搜索框中键入 "Azure Active Directory", 然后选择 " **Azure Active Directory**"。

4. 从 Azure Active Directory 左侧导航菜单中选择 "**企业应用程序**"。

5. 选择“所有应用程序”，查看所有应用程序的列表。

   * 如果在此处看不到所需的应用程序, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件, 并将 "**显示**" 选项设置为 "**所有应用程序"。**

6. 从列表中选择要向其分配用户的应用程序。

7. 加载应用程序后, 从应用程序的左侧导航菜单中选择 "**用户和组**"。

8. 选择 "**用户和组**" 列表顶部的 "**添加**" 按钮, 以打开 "**添加分配**" 窗格。

9. 在“添加分配”窗格中，选择“用户和组”选择器。

10. 键入要在 "**按名称或电子邮件地址**搜索" 搜索框中指定的用户的**完整名称**或**电子邮件地址**。

11. 将鼠标悬停在列表中的**用户**上方以显示**复选框**。 选中用户的个人资料照片或徽标旁边的复选框, 以将用户添加到 "**选定**" 列表。

12. 可选：如果要**添加多个用户**, 请在 "**按名称或电子邮件地址**搜索" 搜索框中键入其他**全名**或**电子邮件地址**, 并选中该复选框以将此用户添加到 "**选定**" 列表。

13. 选择 "用户" 后, 选择 "**选择**" 按钮以将其添加到要分配给应用程序的用户和组的列表中。

14. 可选：选择 "**添加分配**" 窗格中的 "**选择角色**" 选择器, 选择要分配给所选用户的角色。

15. 选择 "**分配**" 按钮, 将应用程序分配给选定用户。

在一段很短的时间后，所选用户能够使用解决方案描述部分中所述的方法启动这些应用程序。

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>自定义发送到应用程序的 SAML 声明

若要了解如何自定义发送到应用程序的 SAML 属性声明, 请参阅[中的声明映射 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)。

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)



