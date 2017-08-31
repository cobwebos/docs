---
title: "将应用程序与 Azure Active Directory 集成 | Microsoft Docs"
description: "有关如何在 Azure Active Directory (Azure AD) 中添加、更新或删除应用程序的详细信息。"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: lenalepa
ms.custom: aaddev
ms.reviewer: luleon
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3be341bcb897a1481f145825429a1a94dfaae3b0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="integrating-applications-with-azure-active-directory"></a>将应用程序与 Azure Active Directory 集成
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

企业开发人员和软件即服务 (SaaS) 提供商可以开发能够与 Azure Active Directory (Azure AD) 集成的商业云服务或业务线应用程序，以针对其服务提供安全的登录和授权。 要将应用程序或服务与 Azure AD 进行集成，开发人员必须首先通过 Azure 经典门户向 Azure AD 注册关于其应用程序的详细信息。

本文说明如何在 Azure AD 中添加、更新或删除应用程序。 将了解有关可与 Azure AD 集成的不同类型的应用程序，以及如何将应用程序配置为访问其他资源（如 Web API 等等）。

若要详细了解表示已注册应用程序的两个 Azure AD 对象及其关系，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)；若要详细了解利用 Azure Active Directory 开发应用程序时应使用的品牌准则，请参阅[适用于集成应用的品牌准则](active-directory-branding-guidelines.md)。

## <a name="adding-an-application"></a>添加应用程序
任何想要使用 Azure AD 功能的应用程序都必须先在 Azure AD 租户中注册。 此注册过程涉及到提供有关应用程序的 Azure AD 详细信息，例如，该应用程序所在位置的 URL、对用户进行身份验证后用于发送答复的 URL、用于标识应用程序的 URI，等等。

如果正在构建的 Web 应用程序只要求支持用户在 Azure AD 中登录，则只需按照以下说明操作。 如果应用程序需要使用凭据或权限访问 Web API，或者需要允许其他 Azure AD 租户中的用户访问该应用程序，请参阅[更新应用程序](#updating-an-application)部分继续配置应用程序。

### <a name="to-register-a-new-application-in-the-azure-portal"></a>在 Azure 门户中注册新应用程序
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在左侧导航窗格中，选择“更多服务”，单击“应用注册”，并单击“添加”。
4. 根据提示创建新的应用程序。 如需 Web 应用程序或本机应用程序的特定示例，请查看[快速入门](active-directory-developers-guide.md)。
  * 对于 Web 应用程序，请在用户登录页面（如 `http://localhost:12345`）提供“登录 URL”，即应用的基 URL。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 对于本机应用程序，请提供“重定向 URI”，Azure AD 将用其返回令牌响应。 输入特定于应用程序的值，例如 `http://MyFirstAADApp`
5. 完成注册后，Azure AD 会为应用程序分配唯一客户端标识符，即应用程序 ID。 现已添加应用程序，此时会转到应用程序的“快速启动”页。 根据应用程序是 Web 应用程序还是本机应用程序，会看到有关如何向应用程序添加更多功能的不同选项。 添加应用程序后，可以开始更新应用程序，使用户能够登录、访问其他应用程序中的 Web API，或者配置多租户应用程序（允许其他组织访问应用程序）。

> [!NOTE]
> 默认情况下，新建的应用程序注册已配置为允许目录中的用户登录到应用程序。
> 
> 

## <a name="updating-an-application"></a>更新应用程序
将应用程序注册到 Azure AD 后，可能需要更新该应用程序，以提供对 Web API 的访问权限、使其可在其他组织中使用，等等。 本部分介绍可以通过哪些不同的方法来进一步配置应用程序。 首先，我们将概述同意框架。如果要构建资源/API 应用程序，并且这些应用程序由组织或其他组织中的开发人员构建的应用程序使用，则必须了解此框架。

有关如何在 Azure AD 中进行身份验证的详细信息，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)。

### <a name="overview-of-the-consent-framework"></a>同意框架概述
使用 Azure AD 的同意架构可以轻松开发需要访问 Azure AD 租户保护的多租户 Web API 的 Web 应用程序和本机客户端应用程序（不同于注册客户端应用程序的框架）。 除了自己的 Web API 之外，这些 Web API 还包括 Microsoft Graph API（用于访问 Azure Active Directory、Intune 以及 Office 365 中的服务）和其他 Microsoft 服务 API。 该框架基于某个用户或管理员，该用户或管理员允许某个应用程序在其目录中注册，这可能涉及到访问目录数据。

例如，如果某个 Web 客户端应用程序需要从 Office 365 中读取关于用户的日历信息，则该用户需要同意该客户端应用程序。 同意后，该客户端应用程序能够代表该用户调用 Microsoft Graph API，并根据需要使用日历信息。 [Microsoft Graph API](https://graph.microsoft.io) 可用来访问 Office 365 中的数据（例如日历、来自 Exchange 的邮件、来自 SharePoint 的站点和列表、来自 OneDrive 的文档、来自 OneNote 的笔记本、来自 Planner 的任务、来自 Excel 的工作簿，等等）、Azure AD 中的用户和组以及更多 Microsoft 云服务中的其他数据对象。 

同意框架使用公共或机密客户端，建立在 OAuth 2.0 及其各种流程的基础之上，例如，代码授权和客户端凭据授权。 通过使用 OAuth 2.0，可以在 Azure AD 中生成多种不同类型的客户端应用程序（例如手机、平板电脑、服务器或 Web 应用程序），并获取对所需资源的访问权限。

有关同意框架的更多详细信息，请参阅 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)、[针对 Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)。有关通过 Microsoft Graph 获取对 Office 365 的授权访问的信息，请参阅[使用 Microsoft Graph 进行应用身份验证](https://graph.microsoft.io/docs/authorization/auth_overview)。

#### <a name="example-of-the-consent-experience"></a>同意体验示例
以下步骤将说明同意体验对应用程序开发人员和用户的工作方式。

1. 在 Azure 门户中 Web 客户端应用程序的配置页上，使用“所需权限”部分中的菜单设置应用程序所需的权限。
   
    ![针对其他应用程序的权限](./media/active-directory-integrating-applications/requiredpermissions.png)
2. 考虑已更新应用程序的权限，该应用程序正在运行，并且某个用户即将首次使用该应用程序。 如果应用程序尚未获取访问令牌或刷新令牌，则该应用程序需要转到 Azure AD 的授权终结点，以获取可用于取得新访问令牌和刷新令牌的授权代码。
3. 如果用户尚未经过身份验证，系统将要求他们登录到 Azure AD。
   
    ![用户或管理员登录到 Azure AD](./media/active-directory-integrating-applications/usersignin.png)
4. 用户登录后，Azure AD 将决定是否要向该用户显示同意页。 此决定基于该用户（或其组织的管理员）是否已授予应用程序许可。 如果尚未授予许可，Azure AD 会提示用户授予许可，并显示运行该应用程序所需的权限。 同意对话框中显示的权限集与在 Azure 门户中的“委派权限”中选择的权限集相同。
   
    ![用户同意体验](./media/active-directory-integrating-applications/consent.png)
5. 用户授予许可后，授权代码将返回到应用程序，应用程序可凭此获取访问令牌和刷新令牌。 有关此流程的详细信息，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)主题中的[从 Web 应用程序到 Web API](active-directory-authentication-scenarios.md#web-application-to-web-api) 部分。

6. 作为管理员，还可以代表租户中的所有用户同意应用程序的委派权限。 这会防止对租户中的每个用户显示同意对话框。 可从 [Azure 门户](https://portal.azure.com)的应用程序页面进行此操作。 从应用程序的“设置”边栏选项卡，单击“所需权限”，并单击“授予权限”按钮。 

    ![授予显式管理许可权限](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> 使用 ADAL.js 的单页面应用程序 (SPA) 当前需要使用“授予权限”按钮授予显式许可，因为请求访问令牌没有许可提示（如果尚未授予许可，会失败）。   

### <a name="configuring-a-client-application-to-access-web-apis"></a>将客户端应用程序配置为访问 Web API
为使 Web/机密客户端应用程序能够参与要求身份验证的授权流程（以及获取访问令牌），必须建立安全凭据。 Azure 门户支持的默认身份验证方法是客户端 ID + 对称密钥。 本部分介绍需要执行哪些配置步骤来提供客户端凭据的机密密钥。

此外，在客户端可以访问资源应用程序公开的 Web API（例如 Microsoft Graph API）之前，同意框架将确保客户端根据请求的权限获取所需的授权。 默认情况下，所有应用程序可以从 Azure Active Directory（图形 API）和 Azure 服务管理 API 中选择权限。默认情况下已选择 Azure AD 的“启用登录并读取用户的配置文件”权限。 如果客户端应用程序已在 Office 365 Azure AD 租户中注册，则也可以选择 SharePoint 与 Exchange Online 的 Web API 和权限。 可以从所需 Web API 旁边的下拉菜单中的[两种类型的权限](active-directory-dev-glossary.md#permissions)中进行选择：

* 应用程序权限：客户端应用程序需要亲自直接访问 Web API（无用户上下文）。 此类型的权限需要管理员同意，并且不可用于本机客户端应用程序。
* 委托的权限：客户端应用程序需要以登录用户的身份访问 Web API，但访问权限受所选权限的限制。 除非权限已配置为需要管理员同意，否则用户可以授予此类型的权限。 

> [!NOTE]
> 将委托权限添加到应用程序不会自动向租户中的用户授予许可，这与在 Azure 经典门户中的情况相同。 用户仍必须手动许可在运行时添加委派权限，除非管理员从 Azure 门户中应用程序页面的“所需权限”部分，单击“授予权限”按钮。 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>添加用于访问 Web API 的凭据或权限
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 若要添加 Web 应用程序凭据的机密密钥，请从“设置”边栏选项卡单击“密钥”部分。  
   
   * 为密钥添加说明，并选择 1 或 2 年持续时间。 
   * 保存配置更改后，最右边的列将包含密钥值。 点击保存后请务必返回到此部分并复制该值，因为在运行时进行身份验证的过程中，需要在客户端应用程序中用到该值。
5. 若要添加用于从客户端访问资源 API 的权限，请从“设置”边栏选项卡单击“所需权限”部分。 
   
   * 首先，单击“添加”按钮。
   * 单击“选择 API”，选择所需的资源类型。
   * 浏览可用 API 列表，或使用搜索框从公开 Web API 的目录中的可用资源应用程序中进行选择。 单击感兴趣的资源，并单击“选择”。
   * 选择后，可移动到“选择权限”菜单，在其中为应用程序选择“应用程序权限”和“委派权限”。
   
6. 完成后，单击“完成”按钮。

> [!NOTE]
> 单击“完成”按钮还会基于配置的“对其他应用程序的权限”自动为目录中的应用程序设置权限。  可以在应用程序“设置”边栏选项卡中查看这些应用程序权限。
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>将资源应用程序配置为公开 Web API
可以开发一个 Web API，并通过公开访问权限[范围](active-directory-dev-glossary.md#scopes)和[角色](active-directory-dev-glossary.md#roles)，使其可供其他客户端应用程序使用。 可以像提供其他 Microsoft Web API（包括图形 API 和 Office 365 API）一样提供正确配置的 Web API。 可通过[应用程序的清单](active-directory-dev-glossary.md#application-manifest)公开访问权限范围和角色。该清单是表示应用程序标识配置的 JSON 文件。  

以下部分说明如何通过修改资源应用程序的清单公开访问范围。

#### <a name="adding-access-scopes-to-your-resource-application"></a>将访问权限范围添加到资源应用程序
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 在应用程序页面中，单击“清单”打开内联清单编辑器。 
5. 使用以下 JSON 代码片段替换“oauth2Permissions”节点。 此示例代码片段演示如何公开称为“用户模拟”的范围，它允许资源所有者为客户端应用程序分配某种类型的、对资源的委托访问权限。 请确保根据自己的应用程序更改文本和值：
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    ID 值必须是使用 [GUID 生成工具](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx)或以编程方式新生成的 GUID。 它表示 Web API 公开的权限的唯一标识符。 将客户端适当地配置为请求访问 Web API 并调用 Web API 后，该客户端会显示一个 OAuth 2.0 JWT 令牌，该令牌的作用域 (scp) 声明已设置为上面指定的值（在本例中为 user_impersonation）。
   
   > [!NOTE]
   > 以后可以根据需要公开其他范围。 请考虑 Web API 可能要公开与各种不同功能关联的多个范围。 现在，可以通过使用所收到的 OAuth 2.0 JWT 令牌中的作用域 (scp) 声明来控制到 Web API 的访问。
   > 
   > 
6. 单击“保存”以保存清单。 此时，Web API 已配置为可供目录中的其他应用程序使用。

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>验证 Web API 是否已公开给目录中的其他应用程序
1. 在顶部菜单中，单击“应用注册”，选择要为其配置 Web API 访问权限的所需客户端应用程序，并导航到“设置”边栏选项卡。
2. 从“所需权限”部分，选择刚为其公开权限的 Web API。 从“委托的权限”下拉菜单中选择新的权限。

![显示“待办事项”权限](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>有关应用程序清单的更多信息
应用程序清单实际上充当了用于更新应用程序实体的机制，它定义了 Azure AD 应用程序的标识配置的所有属性，包括本文所述的 API 访问权限范围。 有关应用程序实体的详细信息，请参阅 [Graph API Application entity documentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)（图形 API 应用程序实体文档）。 在该文档中，可以找到用于指定 API 权限的应用程序实体成员的完整参考信息：  

* appRoles 成员，可用于定义 Web API 的**应用程序权限**的 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 实体集合  
* oauth2Permissions 成员，可用于定义 Web API 的**委托权限**的 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 实体集合

有关应用程序清单概念的一般详细信息，请参阅[了解 Azure Active Directory 应用程序清单](active-directory-application-manifest.md)。

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>通过 Microsoft Graph API 访问 Azure AD Graph 和 Office 365  
如前文所述，除了在自己的资源应用程序上公开/访问 API 以外，还可以更新客户端应用程序以访问 Microsoft 资源公开的 API。  Microsoft Graph API（在其他应用程序的“权限”列表中称为“Microsoft Graph”）可供注册到 Azure AD 的所有应用程序使用。 如果要在 Office 365 预配的 Azure AD 租户中注册客户端应用程序，也可以访问 Microsoft Graph API 向各种 Office 365 资源公开的所有权限。

有关 Microsoft Graph API 公开的所有访问权限范围的完整讨论，请参阅[权限范围 | Microsoft Graph API 概念](https://graph.microsoft.io/docs/authorization/permission_scopes)一文。

> [!NOTE]
> 由于当前存在的限制，如果本机客户端应用程序使用“访问组织的目录”权限，则它们只能调用 Azure AD 图形 API。  此限制不适用于 Web 应用程序。
> 
> 

### <a name="configuring-multi-tenant-applications"></a>配置多租户应用程序
将应用程序添加到 Azure AD 时，可以希望应用程序只能由组织中的用户访问。 或者，你可能希望应用程序可由外部组织中的用户访问。 这两种应用程序分别称为单租户应用程序和多租户应用程序。 可以修改单租户应用程序的配置，使其成为多租户应用程序，本部分将对此进行介绍。

请务必注意单租户应用程序与多租户之间的差别：  

* 单租户应用程序预定在一家组织中使用。 它们通常是企业开发人员编写的业务线 (LoB) 应用程序。 单租户应用程序只需要供单个目录中的用户进行访问，因此，只需要将其设置在单个目录中。
* 多租户应用程序预定在多家组织中使用。 它们通常是独立软件供应商 (ISV) 编写的软件即服务 (SaaS) Web 应用程序。 多租户应用程序需要设置在将使用它们的每个目录中，需要经过用户或管理员许可才能注册它们，并通过 Azure AD 同意框架获得支持。 请注意，默认情况下，所有本机客户端应用程序都是多租户的，因为它们安装在资源所有者的设备上。 有关同意框架的更多详细信息，请参阅上面的“同意框架概述”部分。

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>使外部用户能够向应用程序授予对其资源的访问权限
如果正在编写一个要供组织外部的客户或合作伙伴使用的应用程序，则需要在 Azure 门户中更新应用程序定义。

> [!NOTE]
> 在启用多租户时，必须确保应用程序的应用 ID URI 属于已验证的域。 此外，返回 URL 必须以 https:// 开头。 有关详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)。
> 
> 

要使外部用户能够向应用授予访问权限，请执行以下操作： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 从“设置”边栏选项卡，单击“属性”，将“多租户”开关切换为“是”。

完成上述更改后，其他组织中的用户和管理员可以向应用程序授予对其目录和其他数据的访问权限。

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>在运行时触发 Azure AD 同意框架
若要使用同意框架，多租户客户端应用程序必须使用 OAuth 2.0 请求授权。 我们提供了[代码示例](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)，用于说明 Web 应用程序、本机应用程序或服务器/后台应用程序如何请求授权代码和访问令牌来调用 Web API。

Web 应用程序也可为用户提供注册体验。 如果确实提供注册体验，用户应会单击注册按钮，此时会将浏览器重定向到 Azure AD OAuth2.0 授权终结点或 OpenID Connect userinfo 终结点。 这些终结点允许应用程序通过检查 id_token 来获取有关新用户的信息。 在注册阶段后，用户将看到一个同意提示，与上面“同意框架概述”一节中所示的同意提示类似。

或者，Web 应用程序还可能会提供允许管理员“注册我的公司”的体验。 这种体验也会将用户重定向到 Azure AD OAuth 2.0 授权终结点。 不过在此情况下，会向授权终结点传递一个 prompt=admin_consent 参数来强制提供管理员同意体验，此时管理员会代表其组织授予同意。 只有使用属于全局管理员角色的帐户进行身份验证的用户才能提供同意；其他人会收到错误。 成功同意后，响应中将包含 admin_consent=true。 当换取访问令牌时，还会收到一个 id_token，其中提供了有关注册应用程序的组织和管理员的信息。

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>为单页面应用程序启用 OAuth 2.0 隐式授权
通常将单页面应用程序 (SPA) 构建为一个在浏览器中运行的 JavaScript 重型前端，该前端调用应用程序的 Web API 后端来执行其业务逻辑。 对于托管在 Azure AD 中的 SPA，可以使用 OAuth 2.0 隐式授权对具有 Azure AD 的用户进行身份验证，并获取可用来保护从应用程序 JavaScript 客户端到其后端 web API 的调用的令牌。 用户授予同意之后，可以使用同一个身份验证协议来获取令牌以保护客户端与针对应用程序配置的其他 Web API 资源之间的调用。 若要了解有关隐式授权的详细信息，并帮助确定其是否适合应用程序方案，请参阅[了解 Azure Active Directory 中的 OAuth2 隐式授权流](active-directory-dev-understanding-oauth2-implicit-grant.md)。

默认情况下，为应用程序禁用了 OAuth 2.0 隐式授权。 也可通过在[应用程序清单](active-directory-application-manifest.md)（表示应用程序标识配置的 JSON 文件）中设置 `oauth2AllowImplicitFlow` 值，为应用程序启用 OAuth 2.0 隐式授权。

#### <a name="to-enable-oauth-20-implicit-grant"></a>启用 OAuth 2.0 隐式授权
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 在应用程序页面中，单击“清单”打开内联清单编辑器。
   找到“oauth2AllowImplicitFlow”值并将其设置为“true”。 默认情况下，它是“false”。
   
    `"oauth2AllowImplicitFlow": true,`
5. 保存更新的清单。 保存后，Web API 即已配置为使用 OAuth 2.0 隐式授权来对用户进行身份验证。


## <a name="removing-an-application"></a>删除应用程序
本部分说明如何从 Azure AD 租户中删除应用程序。

### <a name="removing-an-application-authored-by-your-organization"></a>删除组织编写的应用程序
这些应用程序是 Azure AD 租户的“应用程序”主页面上“我的公司拥有的应用程序”筛选器下显示的应用程序。 从技术层面讲，这些是通过 Azure 经典门户以手动方式注册的应用程序，或者通过 PowerShell 或图形 API 以编程方式注册的应用程序。 更具体地说，它们由租户中的应用程序与服务主体对象表示。 有关详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)。

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>从目录中删除单租户应用程序
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 从应用程序页面，单击“删除”。
5. 在确认消息中单击“是”。

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>从目录中删除多租户应用程序
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在顶部菜单中，选择“Azure Active Directory”，单击“应用注册”，并单击要配置的应用程序。 这会转到应用程序的快速启动页面，并打开应用程序的“设置”边栏选项卡。
4. 从“设置”边栏选项卡，选择“属性”，将“多租户”开关切换为“否”。 这会将应用程序转换为单租户应用程序，但该应用程序仍保留在已同意它的组织中。
5. 从应用程序页面，单击“删除”按钮。
6. 在确认消息中单击“是”。

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>删除其他组织授权的多租户应用程序
这些应用程序是 Azure AD 租户的“应用程序”主页面上“我的公司使用的应用程序”筛选器下显示的一部分应用程序，特别是未列在“我的公司拥有的应用程序”列表下的应用程序。 在技术层面讲，它们是在同意处理期间注册的多租户应用程序。 更具体地说，它们仅由租户中的服务主体对象表示。 有关详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)。

公司管理员要删除多租户应用程序对你目录的访问权限（在授予许可后），必须使用 Azure 订阅通过 Azure 门户删除访问权限。 公司管理员也可以使用 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 删除访问权限。

## <a name="next-steps"></a>后续步骤
* 有关应用的视觉指南的提示，请参阅[集成应用的品牌准则](active-directory-branding-guidelines.md)。
* 有关应用程序的应用程序对象与服务主体对象之间关系的详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)。
* 若要了解有关应用程序清单扮演的角色的详细信息，请参阅[了解 Azure Active Directory 应用程序清单](active-directory-application-manifest.md)
* 请参阅 [Azure AD developer glossary](active-directory-dev-glossary.md)（Azure AD 开发人员术语表），了解某些核心的 Azure Active Directory (AD) 开发人员概念的定义。
* 请参阅 [Active Directory 开发人员指南](active-directory-developers-guide.md)，了解与所有开发人员相关内容的概述。


