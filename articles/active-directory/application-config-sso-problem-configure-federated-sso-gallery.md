---
title: 配置 Azure AD 库应用程序的联合单一登录时遇到的问题 | Microsoft Docs
description: 解决在使用 SAML 配置 Azure AD 应用程序库中所列应用程序的联合单一登录时可能遇到的一些常见问题
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
ms.openlocfilehash: 581b3c4ff7cc7b25f238602560f30280efa34edd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053303"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>配置 Azure AD 库应用程序的联合单一登录时遇到的问题

如果在配置应用程序时遇到问题。 验证是否已经遵循应用程序教程中的所有步骤。 在应用程序的配置中，存在关于如何配置应用程序的内联文档。 还可以访问[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，获取详细的分步指南。

## <a name="cant-add-another-instance-of-the-application"></a>无法添加应用程序的另一个实例

若要添加应用程序的第二个实例，需要能够：

-   配置第二个实例的唯一标识符。 配置的这个标识符不能与第一个实例中的标识符相同。

-   配置一个与第一个实例中所用证书不同的证书。

如果应用程序不支持上面的所有配置。 则无法配置第二个实例。

## <a name="cant-add-the-identifier-or-the-reply-url"></a>无法添加标识符或回复 URL

如果无法配置标识符或回复 URL，确认标识符和回复 URL 值是否与应用程序预配置的模式匹配。

若要了解应用程序预配置的模式，请执行以下操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。转到步骤 7。 如果已经位于 Azure AD 上的应用程序配置边栏选项卡中。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  从“模式”下拉列表中选择“基于 SAML 的登录”。

9.  转到“域和 URL”部分下的“标识符”或“回复 URL”文本框。

10. 有三种方法可以了解应用程序支持的模式：

   * 在文本框中，可以看到受支持的模式作为一个占位符。*示例：*<https://contoso.com>。

   * 如果此模式不受支持，当在文本框中试图输入值时会看到一个红色感叹号。 将鼠标悬停在红色感叹号上，就能看到受支持的模式。

   * 在应用程序的教程中，还可以获得受支持的模式的信息。 在“配置 Azure AD 单一登录”部分下。 转到该步骤下，在“域和 URL”部分下找到已配置的值。

如果此值与 Azure AD 上预配置的模式不匹配。 可以：

-   与应用程序供应商沟通以获取与 Azure AD 上预配置的模式匹配的值

-   或者，也可以通过 <aadapprequest@microsoft.com> 联系 Azure AD 团队或在教程中留言，请求更新为应用程序支持的模式

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>我在何处设置 EntityID（用户标识符）格式

无法选择 EntityID（用户标识符）格式，因为用户在进行身份验证后，Azure AD 会将格式发送给响应中的应用程序。

Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>无法找到 Azure AD 元数据来完成应用程序的配置

若要从 Azure AD 下载应用程序元数据或证书，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“SAML 签名证书”部分，并单击“下载”列值。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

Azure AD 不提供用于获取元数据的 URL。 元数据只能作为 XML 文件进行检索。

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>不知道如何自定义发送到应用程序的 SAML 声明

若要了解如何自定义发送到应用程序的 SAML 属性声明，请参阅 [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)，了解详细信息。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](manage-apps/what-is-application-management.md)
