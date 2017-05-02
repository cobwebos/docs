---
title: "配置非库应用程序的联合单一登录时遇到的问题 | Microsoft Docs"
description: "解决在为 Azure AD 应用程序库中所列且为自定义 SAML 的应用程序配置联合单一登录时可能遇到的常见问题"
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
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 5c4ff9ce7dc2c70496b7f7f25de3ee31bd45a662
ms.lasthandoff: 04/18/2017


---

# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>配置非库应用程序的联合单一登录时遇到的问题

如果在配置应用程序时遇到问题。 检查是否遵循了文章[针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)中的所有步骤。

## <a name="cant-add-another-instance-of-the-application"></a>无法添加应用程序的另一个实例

若要添加应用程序的第二个实例，需要能够：

-   配置第二个实例的唯一标识符。 配置的这个标识符不能与第一个实例中的标识符相同。

-   配置一个与第一个实例中所用证书不同的证书。

如果应用程序不支持上面的所有配置。 则无法配置第二个实例。

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>我在何处设置 EntityID（用户标识符）格式

无法选择 EntityID（用户标识符）格式，因为用户在进行身份验证后，Azure AD 会将格式发送给响应中的应用程序。

Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>我从 Azure AD 中的何处获取应用程序元数据或证书

若要从 Azure AD 下载应用程序元数据或证书，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“SAML 签名证书”部分，然后单击“下载”列值。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

Azure AD 不提供用于获取元数据的 URL。 元数据只能作为 XML 文件进行检索。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](active-directory-enable-sso-scenario.md)

