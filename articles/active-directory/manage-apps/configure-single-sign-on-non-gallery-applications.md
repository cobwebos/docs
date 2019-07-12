---
title: 单一登录-非库应用程序-Microsoft 标识平台 |Microsoft Docs
description: Microsoft 标识平台 (Azure AD) 中的非库应用程序配置单一登录 (SSO)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a72cb7bc7feeba984d568a0465d4f23a494496e8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807654"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>在 Microsoft 标识平台中配置单一登录方式登录到非库应用程序

介绍了一项功能，使管理员能够配置单一登录的应用程序在 Microsoft 标识平台应用程序库中缺少*无需编写代码*。

要查找有关如何通过代码将自定义应用与 Azure AD 集成的开发人员指南，请参阅 [Azure AD 的身份验证方案](../develop/authentication-scenarios.md)。

Microsoft 标识平台应用程序库提供了已知能够支持一种形式的单一登录与 Microsoft 标识平台，如中所述的应用程序的列表[这篇文章](what-is-single-sign-on.md)。 IT 专业人员或组织中的系统集成人员找到所要连接的应用程序后，可以遵循 Azure 门户中提供的分步说明启用单一登录。

以下功能，还提供，根据您的许可协议。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

- 使用之类的新式协议的应用程序的自助服务集成[OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols)对其用户进行身份验证并获取令牌，以进行[Microsoft Graph](https://graph.microsoft.com)。
- 支持任何应用程序的自助服务集成[安全断言标记语言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0)标识提供者 （SP 发起或 IdP 发起）
- 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](what-is-single-sign-on.md#password-based-sso) 的任何 Web 应用程序
- 自助服务使用的应用程序的连接[用户预配的跨域标识管理 (SCIM) 协议的系统](use-scim-to-provision-users-and-groups.md)
- 可在 [Office 365 应用启动器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 访问面板](what-is-single-sign-on.md#linked-sign-on)中添加任何应用程序的链接

这些功能可以包括自助服务集成的软件即服务 (SaaS) 应用程序的使用，即使没有人具有载入到 Azure AD 应用程序库的应用程序尚未。 另一项功能是自助服务你的组织已部署到服务器控件，在云或本地中的第三方 web 应用程序集成。

也称为*应用集成模板*，这些功能对于支持 SAML、 SCIM 或基于窗体的身份验证应用程序提供基于标准的连接点。 功能包括灵活的选项和设置与各种应用程序的兼容性。

## <a name="adding-an-unlisted-application"></a>添加未列出的应用程序

Microsoft 标识平台提供了两种机制来注册应用程序。

使用之类的新式协议的应用程序[OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)其用户进行身份验证使用注册[应用程序注册门户](../develop/quickstart-register-app.md)。

若要注册使用所有其他类型的应用程序[支持的身份验证机制](what-is-single-sign-on.md)，如[SAML](../develop/single-sign-on-saml-protocol.md)协议，使用**企业应用程序**边栏选项卡连接它们与 Microsoft 标识平台。

若要连接未列出应用程序使用应用集成模板，请执行以下步骤：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)使用 Microsoft 标识平台管理员帐户。
1. 选择**企业应用程序** > **新的应用程序**。
1. （可选但建议使用）在中**从库中的添加**搜索框中，输入应用程序的显示名称。 如果应用程序出现在搜索结果中，选择它，并跳过此过程的其余部分。
1. 选择**非库应用程序**。 **添加自己的应用程序**页将出现。

   ![显示添加你自己的应用程序页](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)

1. 输入新的应用程序的显示名称。
1. 选择 **添加** 。

通过这种方式添加应用程序，为预先集成的应用程序提供对可用的一个相似的体验。 首先选择**单一登录**从应用程序的侧栏。 下一步的页 (**选择单一登录方法**) 提供了用于配置 SSO 选项：

- **SAML**
- **基于密码**
- **链接**

![显示选择了一个单一登录方法页](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

有关这些选项的详细信息，请参阅本文的以下部分。

## <a name="saml-based-single-sign-on"></a>基于 SAML 的单一登录

选择**SAML**选项配置基于 SAML 的身份验证的应用程序。 （此选项要求的应用程序支持 SAML 2.0。）**设置了单一登录使用 SAML**页将出现。

![显示与 SAML 页的单一登录设置](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

此页包含五个不同的标题：

| 标题编号 | 标题名称 | 此标题的摘要，请参阅： |
| --- | --- | --- |
| 1 | **基本 SAML 配置** | [输入基本 SAML 配置](#enter-basic-saml-configuration) |
| 2 | **用户属性和声明** | [查看或自定义 SAML 令牌中颁发的声明](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML 签名证书** | [查看证书过期数据、 状态和电子邮件通知](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **设置\<应用程序名称 >** | [设置目标应用程序](#set-up-target-application) |
| 5 | **测试单一登录与\<应用程序名称 >** | [测试 SAML 应用程序](#test-the-saml-application) |

现在收集有关如何使用继续操作之前应用程序的 SAML 功能的信息。 完成以下各节中，应用程序与 Azure AD 之间配置 SSO。

### <a name="enter-basic-saml-configuration"></a>输入基本的 SAML 配置

若要设置 Azure AD，请转到**基本 SAML 配置**标题，然后选择其**编辑**图标 （铅笔）。 可以手动输入值，或上传元数据文件以提取字段值。

![显示了基本 SAML 配置页](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

以下两个字段是必需的：

- **标识符**。 此值应唯一标识应用程序的单一登录配置。 您可以找到此值作为**颁发者**中的元素**AuthnRequest**应用程序发送 （SAML 请求）。 该值也在应用程序提供的任何 SAML 元数据中显示为**实体 ID**。 请查看应用程序的 SAML 文档的详细信息及其**实体 ID**或**受众**值。

  下面的代码演示如何**标识符**或**颁发者**应用程序发送到 Azure AD 的 SAML 请求中将显示：

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **回复 URL**。 回复 URL 是应用程序能够接收 SAML 令牌。 此 URL 也称为断言使用者服务 (ACS) URL。 有关 SAML 令牌回复 URL 或 ACS URL 的详细信息，请查看应用程序的 SAML 文档。

  若要配置多个答复 Url，可以使用以下 PowerShell 脚本。

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

以下三个字段是可选的：

- **登录 URL (SP 发起仅)** 。 此值指示用户在这里登录到此应用程序。 如果应用程序执行 SP 启动的 SSO，然后当用户导航到此 URL，SP 执行必要的重定向到 Azure AD 进行身份验证和登录用户。 如果指定此字段时，Azure AD 将使用此 URL 来启动从 Office 365 和 Azure AD 访问面板应用程序。 如果省略此字段时，Azure AD 会执行 IdP 发起的单一登录从 Office 365、 Azure AD 访问面板或 Azure AD SSO URL 的应用程序启动期间 (其中你可以从复制**仪表板**页)。

- **中继状态**。 在 SAML 指示身份验证后将用户重定向的位置的应用程序中，可以指定中继状态。 值通常是 URL 或 URL 将用户带到应用程序中的特定位置的路径。

- **注销 URL**。 此值用于发送 SAML 注销响应返回给应用程序。

有关详细信息，请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)。

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>查看或自定义在 SAML 令牌中颁发的声明

当用户向应用程序身份验证时，Azure AD 颁发应用程序的 SAML 令牌与信息 （或声明） 有关唯一标识用户的信息。 默认情况下，此信息包括用户的用户名、 电子邮件地址、 名字和姓氏。

若要查看或编辑 SAML 令牌发送到应用程序的声明：

- 转到**用户属性和声明**标题，然后选择**编辑**图标。 **用户属性和声明**页将出现。

![显示用户属性和声明页](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

您可能需要编辑 SAML 令牌中颁发以下两个原因的声明：

- 应用程序需要一组不同的声明 URI 或声明值。
- 应用程序所需**标识符值命名**声明以在 Microsoft 标识平台中存储的用户名 （也称为用户主体名称） 以外的其他内容。

有关更多信息，请参阅[如何：自定义的企业应用程序的 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>查看证书过期数据、状态和电子邮件通知

当你创建的库或非库应用程序时，Azure AD 将创建一个特定于应用程序的证书，将从其创建日期的三年到期。 需要使用此证书在 Azure AD 与应用程序之间设置信任。 有关证书格式的详细信息，请参阅应用程序的 SAML 文档。

从 Azure AD，可以下载活动证书中 Base64 或 Raw 格式直接从主**设置了单一登录使用 SAML**页。 或者，可以通过应用联合元数据 URL 下载应用程序元数据 XML 文件，或获取活动证书。

若要查看、 创建或下载证书 （活动或非活动），请转到**SAML 签名证书**标题，然后选择**编辑**图标。 **SAML 签名证书**出现。

![显示 SAML 签名证书页](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

验证证书是否具有：

- *所需的到期日期。* 在未来，可以配置最多三年的到期日期。
- *为活动状态的所需的证书。* 如果状态为**非活动**，将状态更改为**Active**。 若要更改的状态，右键单击所需的证书的行，然后选择**使证书处于活动状态**。
- *正确签名的选项和算法。*
- *正确的通知电子邮件地址。* 如果活动证书接近到期日期，Azure AD 配置在此字段中的电子邮件地址发送通知。  

有关详细信息，请参阅[管理联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)并[高级的证书签名的 SAML 令牌中的选项](certificate-signing-options.md)。

### <a name="set-up-target-application"></a>设置目标应用程序

若要配置 SSO 的应用程序，找到应用程序的文档。 若要查找的文档，请转到**设置\<应用程序名称 >** 标题，然后选择**查看分步说明**。 文档会显示在**配置单一登录**页。 该页面可指导您在填写**登录 URL**， **Azure AD 标识符**，并**注销 URL**中的值**设置\<应用程序名称>** 标题。

所需值根据应用程序的不同而异。 有关详细信息，请参阅应用程序的 SAML 文档。 **登录 URL**并**注销 URL**均解析为同一终结点，为 Azure AD 的实例是 SAML 请求处理终结点的值。 **Azure AD 标识符**的值**颁发者**SAML 令牌中颁发给应用程序。

### <a name="assign-users-and-groups-to-your-saml-application"></a>将用户和组分配到 SAML 应用程序

配置应用程序以使用 Azure AD 作为基于 SAML 的标识提供者后，它是基本准备好测试。 作为安全控制机制，Azure AD 只能颁发令牌允许用户登录到应用程序，如果 Azure AD 已向用户授予访问权限。 直接或通过组成员身份，用户可能获得访问权限。

若要将新用户或组分配到你的应用程序：

1. 在应用程序边栏中，选择**用户和组**。 **\<应用程序名称 >-用户和组**页出现后，它显示了已分配的用户和组的当前列表。
1. 选择**将用户添加**。 **添加分配**页将出现。
1. 选择**用户和组 (\<数 > 所选)** 。 **用户和组**页出现后，显示可用的用户和组的列表。
1. 若要查找的用户或组您想要从列表中分配的类型或滚动。
1. 选择每个用户或组你想要添加，并选择**选择**按钮。 **用户和组**页将消失。
1. 在中**添加分配**页上，选择**分配**。 **\<应用程序名称 >-用户和组**页将显示在列表中的其他用户显示。

   ![显示应用程序的用户和组页面](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

在此列表中，你可以：

- 删除用户。
- 编辑其角色。
- 更新其凭据 （用户名和密码），以便用户可以对从用户的访问面板中的应用程序进行身份验证。

可以编辑或一次删除多个用户或组。

将用户分配允许 Azure AD 颁发的令牌的用户。 此外，还会在用户的访问面板中显示此应用程序的磁贴。 此外，如果用户正在使用 Office 365，Office 365 应用程序启动器中也会显示应用程序磁贴。

> [!NOTE]
> 可以在应用程序的“配置”选项卡中使用“上传徽标”按钮来上传应用程序的磁贴徽标。  

### <a name="test-the-saml-application"></a>测试 SAML 应用程序

测试 SAML 应用程序之前，您必须具有已设置与 Azure AD 应用程序，同时分配到应用程序的用户或组。 若要测试 SAML 应用程序，请选择**单一登录**，将返回到**基于 SAML 的单一登录**页。 (如果不同的 SSO 方法有效，则选择**更改单一登录模式** > **SAML**太。)然后在**单一登录与测试\<应用程序名称 >** 标题下方，选择**测试**。 有关详细信息，请参阅[调试基于 SAML 的单一登录-登录到 Azure Active Directory 中的应用程序](../develop/howto-v1-debug-saml-sso-issues.md)。

## <a name="password-single-sign-on"></a>密码单一登录

选择此选项以配置[基于密码的单一登录](what-is-single-sign-on.md)为具有 HTML 登录页的 web 应用程序。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 还有适用于多个用户需要共享单个帐户，例如共享组织的社交媒体应用帐户的方案。

选择后**基于密码的**，系统会提示你输入的登录页中的基于 web 的应用程序的 URL。

![显示了单一登录 URL 页后，可以输入登录 URL](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

然后执行以下步骤：

1. 输入的 URL。 此字符串必须是包含用户名输入的字段的页。
1. 选择**保存**。 Azure AD 会尝试分析输入的用户名和密码输入的登录页。
1. 如果 Azure AD 的分析过程中尝试失败，则选择**配置\<应用程序名称 > 密码单一登录设置**以显示**配置单一登录**页。 （如果该尝试成功，你可以忽略此过程的其余部分）。
1. 选择**手动检测登录字段**。 将显示描述手动检测登录字段的其他说明。

   ![手动配置的基于密码的单一登录](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)

1. 选择**捕获登录字段**。 捕获状态页将打开新选项卡，显示的消息**元数据捕获当前正在进行**。
1. 如果**需要的访问面板扩展**框出现在新的选项卡上，选择**立即安装**安装**我的应用安全登录扩展**浏览器扩展。 （浏览器扩展需要 Microsoft Edge、 Chrome 或 Firefox）。然后安装、 启动，并启用扩展，并刷新捕获状态页。

   然后，浏览器扩展打开显示输入的 URL 的另一个选项卡。

1. 在输入的 URL 与选项卡上，转完成登录过程。 填写用户名和密码字段，并在尝试登录。 （您无需提供正确的密码。）

   一个提示，询问你保存捕获登录字段。

1. 选择“确定”  。 选项卡关闭，则浏览器扩展并显示消息更新捕获状态页**元数据已更新的应用程序**，和选项卡也将关闭该浏览器。
1. 在 Azure AD**配置单一登录**页上，选择**好了，我就能够登录到应用程序已成功**。
1. 选择“确定”  。

后的登录页捕获，您可能会为用户和组，并可以设置凭据策略，就像常规[密码 SSO 应用程序](what-is-single-sign-on.md)。

> [!NOTE]
> 可以在应用程序的“配置”选项卡中使用“上传徽标”按钮来上传应用程序的磁贴徽标。  

## <a name="existing-single-sign-on"></a>现有的单一登录

选择此选项可将链接添加到你组织的 Azure AD 访问面板或 Office 365 门户中的应用程序。 您可以使用此方法将链接添加到当前使用 Active Directory 联合身份验证服务 （或其他联合身份验证服务） 的自定义 web 应用程序而不是 Azure AD 进行身份验证。 或者，可以添加特定 SharePoint 页面或想要在用户访问面板上显示的其他网页的深层链接。

选择后**链接**，系统会提示您输入要链接到的应用程序的 URL。 键入的 URL，然后选择**保存**。 可以将用户和组分配给应用程序，使应用程序中出现[Office 365 应用启动器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或[Azure AD 访问面板](end-user-experiences.md)为这些用户。

> [!NOTE]
> 可以在应用程序的“配置”选项卡中使用“上传徽标”按钮来上传应用程序的磁贴徽标。  

## <a name="related-articles"></a>相关文章

- [如何：自定义的企业应用程序的 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)
- [调试基于 SAML 的单一登录到 Azure Active Directory 中的应用程序](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft 标识平台 (以前称为 Azure Active Directory 为开发人员)](../develop/index.yml)
