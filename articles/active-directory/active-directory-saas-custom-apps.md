---
title: "为应用程序配置 Azure AD SSO | Microsoft 文档"
description: "了解如何使用 SAML 和基于密码的 SSO 通过自助方式将应用连接到 Azure Active Directory"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: asmalser
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 18415c92d50a00c14823685857ab7e2624334ec7
ms.openlocfilehash: b4a1bb3211da8c02d48ebad69d5e1cbb4de2c45d
ms.lasthandoff: 03/01/2017


---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录
本文介绍可让管理员在*不编写任何代码*的情况下，针对不在 Azure Active Directory 应用库中的应用程序配置单一登录的功能。 此功能已在 2015 年 11 月 18 日技术预览中发布，并且已包含在 [Azure Active Directory Premium](active-directory-editions.md) 中。 若要查找有关如何通过代码将自定义应用与 Azure AD 集成的开发人员指南，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)。

如[此文](active-directory-appssoaccess-whatis.md)中所述，Azure Active Directory 应用程序库提供了一份已知能够支持 Azure Active Directory 单一登录的应用程序列表。 IT 专业人员或组织中的系统集成人员找到所要连接的应用程序后，可以遵循 Azure 管理门户中提供的分步说明启用单一登录。

具有 [Azure Active Directory Premium](active-directory-editions.md) 许可证的客户还会额外获得以下功能：

* 通过自助方式集成支持 SAML 2.0 标识提供者的任何应用程序（SP 发起或 IdP 发起）
* 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) 的任何 Web 应用程序
* 以自助方式连接使用 SCIM 协议进行用户预配的应用程序（如[此处所述](active-directory-scim-provisioning.md)）
* 可在 [Office 365 应用启动器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 访问面板](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中添加任何应用程序的链接

这不仅包括正在使用、但尚未登记到 Azure AD 应用程序库中的 SaaS 应用程序，也包括组织已部署到控制的服务器（在云中或本地）的第三方 Web 应用程序。

这些功能（也称为*应用集成模板*）为支持 SAML、SCIM 或基于窗体的身份验证的应用程序提供了基于标准的连接点，包括灵活的选项以及与各种应用程序兼容的设置。 

## <a name="adding-an-unlisted-application"></a>添加未列出的应用程序
若要使用应用集成模板连接应用程序，请使用 Azure Active Directory 管理员帐户登录到 Azure 管理门户，导航到“Active Directory”> [目录] >“应用程序”部分，选择“添加”，然后选择“从库中添加应用程序”。 

![][1]

在应用库中，可以使用左侧的“自定义”类别来添加未列出的应用程序；找不到所需的应用时，可以选择搜索结果中显示的“添加未列出的应用程序”链接来添加该应用。 输入应用程序的名称后，便可以配置单一登录选项和行为。 

**快速提示**：最佳实践是使用搜索函数来检查该应用程序是否已在应用程序库中。 如果找到该应用并且其说明中提到“单一登录”，则表示应用程序支持联合单一登录。 

![][2]

以这种方式添加应用程序非常类似于针对预先集成的应用程序提供的体验。 若要开始，请选择“配置单一登录”。 随后出现的屏幕将显示以下三个单一登录配置选项，后续部分将介绍这些选项。

![][3]

## <a name="azure-ad-single-sign-on"></a>Azure AD 单一登录
选择此选项可为应用程序配置基于 SAML 的身份验证。 此选项要求应用程序支持 SAML 2.0。在继续下一步之前，应该先收集有关如何使用应用程序的 SAML 功能的信息。 选择“下一步”后，系统将提示输入对应于应用程序 SAML 终结点的三个不同的 URL。 

![][4]

其中包括：

* **登录 URL (仅限 SP 发起)** – 用户可通过此 URL 登录到此应用程序。 如果将应用程序配置为执行服务提供者发起的单一登录，则当用户导航到此 URL 时，服务提供者会进行必要的重定向，重定向到 Azure AD 完成用户身份验证和登录。 如果填充了此字段，则 Azure AD 会使用此 URL 从 Office 365 和 Azure AD 访问面板启动应用程序。 如果忽略此字段，则从 Office 365、Azure AD 访问面板或 Azure AD 单一登录 URL（从“仪表板”选项卡复制）启动应用时，Azure AD 会执行标识提供者发起的登录。
* **颁发者 URL** - 颁发者 URL 应唯一标识正在为其配置单一登录的应用程序。 这是 Azure AD 发送回应用程序的值，作为 SAML 令牌的 **Audience** 参数，预计由应用程序对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为**实体 ID**。 有关实体 ID 或 Audience 值的详细信息，请查看应用程序的 SAML 文档。 以下示例显示了在返回到应用程序的 SAML 令牌中的受众 URL 的形式：

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **回复 URL** - 应用程序通常会在回复 URL 中接收 SAML 令牌。 也称**断言使用者服务 (ACS) URL**。 有关 SAML 令牌回复 URL 或 ACS URL 的详细信息，请查看应用程序的 SAML 文档。
  输入这些信息后，单击“下一步”转到下一屏幕。 该屏幕说明需在应用程序端进行什么配置才能接受来自 Azure AD 的 SAML 令牌。 

![][5]

所需值根据应用程序的不同而异，请查看应用程序的 SAML 文档获取详细信息。 **登录**和**注销**服务 URL 解析为相同的终结点，即 Azure AD 实例的 SAML 请求处理终结点。 颁发者 URL 值在颁发给应用程序的 SAML 令牌中显示为“Issuer”。 

配置应用程序后，请单击“下一步”按钮，然后单击“完成”关闭对话框。 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>将用户和组分配到 SAML 应用程序
将应用程序配置为使用 Azure AD 作为基于 SAML 的标识提供者后，便可以开始测试。 作为安全控制机制，Azure AD 不会发出可让用户登录应用程序的令牌，除非使用 Azure AD 向他们授予了访问权限。 可以直接向用户授予访问权限，或者通过他们所属的组授予访问权限。 

若要将用户或组分配到应用程序，请单击“分配用户”按钮。 选择要分配的用户或组，然后选择“分配”按钮。 

![][6]

分配用户后，Azure AD 可为该用户颁发令牌，并在用户的访问面板中显示此应用程序的磁贴。 如果用户使用 Office 365，则 Office 365 应用程序启动器中也会显示一个应用程序磁贴。 

可以在应用程序的“配置”选项卡中使用“上载徽标”按钮来上载应用程序的磁贴徽标。 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>自定义在 SAML 令牌中颁发的声明
当用户在应用程序中进行身份验证时，Azure AD 会将一个 SAML 令牌颁发给应用，其中包含用于唯一标识用户的用户相关信息（或声明）。 默认情况下，这些信息包括用户的用户名、电子邮件地址、名字和姓氏。 

可以在“属性”选项卡下面查看或编辑通过 SAML 令牌发送到应用程序的声明。 

![][7]

你可能出于两个原因编辑 SAML 令牌中颁发的声明：•被写入的应用程序需要一组不同的声明 URI 或声明值 •应用程序的部署方式需要 NameIdentifier 声明不同于存储在 Azure Active Directory 中的用户名（也称为用户主体名称）。 

有关如何针对方案添加和编辑声明的信息，请参阅这篇[有关声明自定义的文章](active-directory-saml-claims-customization.md)。 

### <a name="testing-the-saml-application"></a>测试 SAML 应用程序
在 Azure AD 和应用程序中配置 SAML URL 与证书、将用户或组分配到 Azure 中的应用程序，并根据需要查看和编辑声明之后，用户即可登录到应用程序。 

若要进行测试，请在 https://myapps.microsoft.com 上使用分配给应用程序的用户帐户登录到 Azure AD 访问面板，然后单击应用程序磁贴开始单一登录过程。 或者，可以直接导航到应用程序的登录 URL 并从中登录。 

有关调试提示，请参阅这篇[有关如何调试对应用程序进行基于 SAML 的单一登录的文章](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>密码单一登录
选择此选项可为包含 HTML 登录页的 Web 应用程序配置[基于密码的单一登录](active-directory-appssoaccess-whatis.md)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。 

选择“下一步”后，系统会提示输入应用程序的 Web 登录页的 URL。 请注意，此登录页必须是包含用户名和密码输入字段的页面。 输入此信息后，Azure AD 将启动在登录页中分析用户名输入和密码输入的过程。 如果该过程不成功，系统将引导你完成另一个过程，安装一个浏览器扩展（需要使用 Internet  Explorer、Chrome 或 Firefox）来手动捕获字段值。

捕获登录页后，可以分配用户和组，并且可以像在[密码 SSO 应用](active-directory-appssoaccess-whatis.md)中一样设置凭据策略。

注意：可以在应用程序的“配置”选项卡中使用“上载徽标”按钮来上载应用程序的磁贴徽标。 

## <a name="existing-single-sign-on"></a>现有的单一登录
选择此选项可将应用程序的链接添加到组织的 Azure AD 访问面板或 Office 365 门户。 可以使用此选项来添加当前正在使用 Azure Active Directory 联合身份验证服务（或其他联合身份验证服务）而不是 Azure AD 进行身份验证的自定义 Web 应用的链接。 或者，可以添加特定 SharePoint 页面或想要在用户访问面板上显示的其他网页的深层链接。 

选择“下一步”后，系统会提示输入要链接到的应用程序的 URL。 完成上述操作后，可将用户和组分配到应用程序，使应用程序显示在这些用户的 [Office 365 应用启动器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 访问面板](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中。

注意：可以在应用程序的“配置”选项卡中使用“上载徽标”按钮来上载应用程序的磁贴徽标。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [如何为预先集成的应用自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)
* [排查基于 SAML 的单一登录问题](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

