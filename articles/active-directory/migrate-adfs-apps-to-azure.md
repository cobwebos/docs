---
title: "将 AD FS 本地应用迁移到 Azure。 | Microsoft Docs"
description: "本文档旨在帮助组织了解如何将本地应用程序迁移到 Azure AD，并着重介绍联合 SaaS 应用程序。"
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: billmath
ms.openlocfilehash: f1256e2fefe1ad3d37403f37804888ab9dcf6e99
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>将 AD FS 本地应用迁移到 Azure 

本文档旨在帮助组织了解如何将本地应用程序迁移到 Azure AD，  并着重介绍联合 SaaS 应用程序。  本文档不提供分步指南，  而是提供帮助你实现迁移的概念性指南，让你了解如何将本地配置转换为 Azure AD。 另外还介绍了最常用方案的要求。

## <a name="introduction"></a>介绍

如果本地目录包含用户帐户，你可能已经有至少一到两个应用。  这些应用已配置为让用户在访问时通过这些标识登录。

如果像大多数组织一样，你可能正准备采用云应用程序和标识。  也许你正在使用 Office 365 和 Azure AD Connect 启动并运行某个应用程序。  也许你已经为某些但并非所有关键工作负荷设置了基于云的 SaaS 应用程序。  

许多组织的 SaaS 或自定义业务线 (LoB) 应用都与本地登录服务（例如 Active Directory 联合身份验证服务 (AD FS)、Office 365 以及基于 Azure AD 的应用）直接联合。  本迁移指南介绍将本地应用程序迁移到 Azure AD 的原因和方法。

>[!NOTE]
>本指南详细介绍了 SaaS 应用配置和迁移，并大致介绍了自定义 LoB 应用。  将来计划推出自定义 LoB 应用的更详细指南。

图 1：在本地直接连接的应用 ![本地](media/migrate-adfs-apps-to-azure/migrate1.png)

图 2：通过 Azure AD 进行联合的应用 ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>将应用迁移到 Azure AD 的原因

对于已使用 AD FS、Ping 或其他本地身份验证提供程序的组织，将应用迁移到 Azure AD 有以下好处：

**访问更安全**
- 使用 [Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)配置细致的按应用程序访问控制，包括多重身份验证 (MFA)。  这些策略应用到 SaaS 和自定义应用的方式可以与现在对 Office 365 应用策略的方式相同
- 若要根据用于确定危险流量的机器学习和试探法来检测威胁并保护登录，请使用 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)，充分利用 Azure AD 的内置且持续改进的功能

**Azure AD B2B 协作**
- 确定登录到 SaaS 应用是基于 Azure AD 以后，即可使用 [Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)授予合作伙伴对云资源的访问权限。

**更轻松的 Azure AD 管理体验和更多 Azure AD 功能**
- 作为 SaaS 应用的标识提供程序，Azure AD 支持更多的功能，例如基于应用程序的令牌签名证书、[可配置的证书到期日期](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)，以及根据 Azure AD 标识[自动预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)用户帐户（在关键的库应用中进行）

**保留本地标识提供程序的优点**
- 在获得 Azure AD 优势的同时，可以继续使用本地解决方案进行身份验证，因此仍可继续利用本地多重身份验证 (MFA) 解决方案、日志记录、审核等方面的优势 

**在停用本地标识提供程序后实现快速开始**
- 若要停用本地身份验证产品，组织可以将应用迁移到 Azure AD，这样即可节省部分工作，从而轻松地进行转换 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>将本地应用类型映射到 Azure AD 中的应用类型
可以根据所用登录类型将大多数应用划分到多个类别中的一个。  这些类别决定了应用在 Azure AD 中的呈现方式。

简单地说，SAML 2.0 应用程序可以通过 Azure AD 应用程序库与 Azure AD 集成，也可以作为非库应用程序进行集成。  类似地，使用 OAuth 2.0 或 OpenID Connect 的应用可以以“应用注册”的方式与 Azure AD 集成。  阅读更多详细信息。

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>联合 SaaS 应用与自定义 LoB 应用
联合应用包括归到所列类别中的应用。

- SaaS 应用 
    - 如果你的用户登录到 SaaS 应用（例如 Salesforce、ServiceNow 或 Workday），而你集成到本地标识提供程序（例如 AD FS 或 Ping），则你使用的是适用于 SaaS 应用的联合登录。
    - 应用通常使用 SAML 2.0 协议进行联合登录。
    - 归到此类别的应用程序可以通过库以企业应用程序的方式与 Azure AD 集成，也可以作为非库应用程序进行集成。
- 自定义 LOB 应用程序
    - 这是指非 SaaS 应用。此类应用由组织内部开发，或者作为标准打包产品提供，安装在数据中心。  此类应用包括 SharePoint 应用以及基于 Windows Identity Foundation (WIF) 生成的应用。
    - 应用可以使用 SAML 2.0、WS-Federation、OAuth 或 OpenID Connect 进行联合登录
    - 使用 Oauth 2.0、OpenID Connect 或 WS 联合身份验证的自定义应用可以以“应用注册”的方式与 Azure AD 集成，使用 SAML 2.0 或 WS 联合身份验证的自定义应用可以在企业应用程序中作为非库应用程序集成

### <a name="non-federated-apps"></a>非联合应用
另外，非联合应用可以使用 Azure AD 应用程序代理和相关功能与 Azure AD 集成。  有关这些功能的详细信息，请查看链接：
- 直接使用 Windows 集成身份验证 (WIA) 向 Active Directory 验证身份的应用
    - 这些应用可以通过 [Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)集成到 Azure AD
- 通过代理与单一登录提供程序集成并使用标头进行授权的应用
    - 可以对使用已安装代理进行登录并使用基于标头的授权的本地应用进行配置，以便将 Azure AD 应用程序代理与 [Ping Access for AzureAD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/) 配合使用，实现基于 Azure AD 的登录

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>将本地联合应用转换为 Azure AD 
幸运的是，AD FS 和 Azure AD 的工作原理类似，因此配置信任、登录和登出 URL 以及标识符的概念在两种情况下都适用。  但是，在进行转换时，需要了解一些小差异。

在表中，我们映射了多个由 AD FS、Azure AD 和 SaaS 应用共享的重要概念，帮助你进行转换。 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>在 Azure AD 或 AD FS 中呈现应用
迁移开始时，会评估应用程序在本地的配置方式并将该配置映射到 Azure AD。  下表将 AD FS 信赖方配置元素映射到 Azure AD 中的相应元素。  
- AD FS 术语：信赖方或信赖方信任
- Azure AD 术语：企业应用程序或应用注册（具体取决于应用类型）

|应用配置元素|说明|在 AD FS 配置中|在 Azure AD 配置中的相应位置|SAML 令牌元素|
|-----|-----|-----|-----|-----|
|应用登录 URL|此应用程序的登录页的 URL。 这是用户进入后会在 SP 启动的 SAML 流中启动应用登录的位置。|不适用|在 Azure AD 中，登录 URL 在 Azure 门户中配置，具体说来是在应用程序的“单一登录属性”中作为“登录 URL”配置。</br></br>（可能需要单击“显示高级 URL 设置”才能看到登录 URL）||
|应用回复 URL|从 IdP 的角度来看应用的 URL。  这是在用户于 IDP 处登录以后，发送用户和令牌的位置。</br></br>  这有时称为 SAML 断言使用方终结点。|存在于应用的 AD FS 信赖方信任中。  右键单击信赖方，然后选择“属性”->“终结点”选项卡。|在 Azure AD 中，回复 URL 在 Azure 门户中配置，具体说来是在应用程序的“单一登录属性”中作为“回复 URL”配置。</br></br>（可能需要单击“显示高级 URL 设置”才能看到回复 URL）|映射到 SAML 令牌中的 Destination 元素。</br></br>  示例值：https://contoso.my.salesforce.com|
|应用注销 URL|一个 URL，当用户从应用注销时会向其发送“注销清理”请求，以便注销 IDP 已将用户登录到其中的所有其他应用。|存在于“AD FS 管理”中的“信赖方信任”下。  右键单击 RP，选择“属性”-> 单击“终结点”选项卡|不适用 - Azure AD 不支持“单一注销”（即注销所有应用）。  它只将用户从 Azure AD 注销。|不可用|
|应用标识符|应用的标识符（从 IdP 的角度来看）。通常使用登录 URL 值作为标识符（但也不一定）</br></br>  应用有时将其称为“实体 ID”。|在 AD FS 中，此项为信赖方 ID：右键单击信赖方信任，选择“属性”-> 单击“标识符”选项卡|在 Azure AD 中，标识符是在 Azure 门户中配置的，具体说来是在应用程序的“单一登录属性”中作为“标识符”在“域和 URL”下配置的（可能需要单击“显示高级 URL 设置”复选框）|对应于 SAML 令牌中的 Audience 元素|
|应用联合元数据|应用的联合元数据的位置。  由 IdP 用来自动更新特定的配置设置，例如终结点或加密证书。|应用的联合元数据 URL 存在于应用的 AD FS 信赖方信任中。  右键单击信任，选择“属性”，然后单击“监视”选项卡。|不适用 - Azure AD 不直接支持使用应用程序联合元数据|不可用|
|用户标识符/NameID|一个属性，用于以唯一方式向应用指示 Azure AD 或 AD FS 中的用户标识。</br></br>  通常为用户的 UPN 或电子邮件地址。|在 AD FS 中，此项充当信赖方的声明规则。  大多数情况下，声明规则在发出声明时，其类型以“nameidentifier”结尾|在 Azure AD 中，用户标识符位于 Azure 门户的应用程序“单一登录属性”的“用户属性”标头下。</br></br>默认使用 UPN。|在 SAML 令牌中作为“NameID”元素从 IDP 传送到应用。|
|要发送到应用的其他声明|除了用户标识符/NameID，通常还会将其他声明信息从 IDP 发送到应用，例如名字、姓、电子邮件地址、用户所在的组|在 AD FS 中，此项充当信赖方的其他声明规则。|在 Azure AD 中位于 Azure 门户的应用程序“单一登录属性”的“用户属性”标头下。请单击“查看和编辑其他所有用户属性”。|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>在 SaaS 应用中以标识提供者 (IdP) 身份呈现 Azure AD
在迁移过程中，需将应用配置为指向 Azure AD（而不是本地标识提供者）。  此部分主要介绍使用 SAML 协议的 SaaS 应用，而不是自定义/LOB 应用。 但是，所述概念会扩展到自定义/LOB 应用。 

在高级别将 SaaS 应用指向 Azure AD 时，有一些需要注意的重要事项
- 标识提供者颁发者：https&#58;//sts.windows.net/{tenant-id}/
- 标识提供者登录 URL：https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- 标识提供者注销 URL：https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- 联合元数据位置：https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

其中，{tenant-id} 将替换为你的租户 ID，该 ID 在 Azure 门户的 Azure Active Directory ->“属性”下显示为“目录 ID”；{application-id} 将替换为你的应用程序的 ID，该 ID 在应用程序的“属性”下显示为“应用程序 ID”

下表更详细地介绍了在应用中配置 SSO 设置时的关键 IdP 配置元素，以及其在 AD FS 和 Azure AD 中的值或位置。  表的参考框架为 SaaS 应用，该应用需要知道将身份验证请求发送到何处，以及如何验证收到的令牌。

|配置元素|说明|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>登录 </br>代码|从应用的角度来看，IdP 的登录 URL（将用户重定向到其中以便进行登录的位置）。|AD FS 登录 URL 是 AD FS 联合身份验证服务名称后跟“/adfs/ls/”，例如：https&#58;//fs.contoso.com/adfs/ls/|Azure AD 的相应值遵循以下模式，其中的 {tenant-id} 将替换为你的租户 ID，该 ID 在 Azure 门户中的 Azure Active Directory ->“属性”下显示为“目录 ID”。</br></br>对于使用 SAML-P 协议的应用：https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>对于使用 WS 联合身份验证协议的应用：https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|IdP </br>注销 </br>代码|从应用的角度来看，IdP 的注销 URL（当用户选择“注销”应用时将用户重定向到其中的位置）。|对于 AD FS，注销 URL 是与登录 URL 相同的 URL，或者是附加了“wa=wsignout1.0”的同一 URL，例如：https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|Azure AD 的相应值取决于应用程序是否能够支持 SAML 2.0 注销。</br></br>如果应用支持 SAML 注销，则该值遵循以下模式，其中，{tenant-id} 的值将替换为租户 ID，该 ID 在 Azure 门户中的 Azure Active Directory ->“属性”下显示为“目录 ID”。 https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>如果应用不支持 SAML 注销：https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|令牌 </br>签名 </br>证书|一种证书，IDP 使用其私钥来签署颁发的令牌。  验证令牌是否来自根据配置应用所信任的 IDP。|AD FS 令牌签名证书位于 AD FS 管理中的“证书”下。|在 Azure AD 中，令牌签名证书位于 Azure 门户中，具体说来是在应用程序的“单一登录属性”中的“SAML 签名证书”标头下，可以在其中下载要上传到应用的证书。</br></br>  如果应用程序有多个证书，则可在联合元数据 xml 文件中找到所有证书。|
|标识符/ </br>“颁发者”|从应用的角度来看，IdP 的标识符（有时称为“颁发者”或“颁发者 ID”）</br></br>在 SAML 令牌中，此值显示为“Issuer”元素|AD FS 的标识符通常是 AD FS 管理中的联合身份验证服务标识符，位于“服务”->“编辑联合身份验证服务属性”下。  例如：http&#58;//fs.contoso.com/adfs/services/trust|Azure AD 的相应值遵循以下模式，其中，{tenant-id} 的值将替换为租户 ID，该 ID 在 Azure 门户中的 Azure Active Directory ->“属性”下显示为“目录 ID”。  https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>联合 </br>元数据|IDP 的公开提供的联合元数据的位置。  （某些应用使用联合元数据来分别代替管理员配置 URL、标识符、令牌签名证书）|AD FS 联合元数据 URL 位于 AD FS 管理中的“服务”->“终结点”->“元数据”->“类型: 联合元数据”下，例如：https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Azure AD 的相应值遵循 https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml 模式，其中，{TenantDomainName} 的值将替换为“contoso.onmicrosoft.com”格式的租户名称 </br></br>Azure AD 中联合元数据的[详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)。

## <a name="migrating-saas-apps"></a>迁移 SaaS 应用
目前，将 SaaS 应用从 AD FS 或其他标识提供者迁移到 Azure AD 中需要手动进行。 如需特定于应用的指南，请[查看介绍如何集成库中的 SaaS 应用的教程的列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)。

这些集成教程假定你在进行绿色字段集成。  在计划、评估、配置和直接转换应用时，应该了解一些特定于迁移的重要概念。  
- 虽然某些应用可以轻松进行迁移，但那些具有较复杂要求（例如自定义声明）的应用可能需要在 Azure AD 和/或 Azure AD Connect 中进行其他配置
- 在最常用方案中，应用只需要 NameId 声明和其他常用的用户标识符声明；若要确定是否需要其他声明，请检查通过 AD FS 或第三方标识提供者颁发的具体声明
- 确定需要其他声明以后，需确保这些声明在 Azure AD 中可用。  需检查 Azure AD Connect 同步配置，确保将所需属性（例如 samAccountName）同步到 Azure AD
- 属性在 Azure AD 中可用以后，请在 Azure AD 中添加声明颁发规则，以便将这些属性作为声明包括在颁发的令牌中。  在 Azure AD 的应用的“单一登录属性”中完成相关操作。

### <a name="assessing-what-can-be-migrated"></a>评估可迁移的内容
SAML 2.0 应用程序可以通过 Azure AD 应用程序库与 Azure AD 集成，也可以作为非库应用程序进行集成。  

某些配置需要额外的步骤才能在 Azure AD 中进行配置，某些配置目前不受支持。  若要确定哪些内容可以移动，请查看每个应用的当前配置，尤其是以下配置：
- 配置的声明规则（颁发转换规则）
- SAML NameID 格式和属性
- 颁发的 SAML 令牌版本
- 其他配置，例如，颁发授权规则或访问控制策略和多重身份验证（其他身份验证）规则

#### <a name="what-can-be-migrated-today"></a>目前可迁移的内容
目前可以轻松迁移的应用包括 SAML 2.0 应用，这些应用使用配置元素和声明的标准集。  这些应用可以包含
- 用户主体名称
- 电子邮件地址
- 名字
- Surname
- 充当 SAML NameID 的备用属性，包括 Azure AD 邮件属性、邮件前缀、employeeid、扩展属性 1 - 15，或者本地 SamAccountName（请参阅[编辑 NameIdentifier 声明）](./develop/active-directory-saml-claims-customization.md)
- 自定义声明（请参阅[此处](active-directory-claims-mapping.md)和[此处](./develop/active-directory-saml-claims-customization.md)的文档，了解支持的声明映射）

除了自定义声明和 nameID 元素，在迁移过程中需要在 Azure AD 中执行其他配置步骤的配置包括：
- AD FS 中的自定义授权或 MFA 规则（使用 [Azure AD 条件访问](active-directory-conditional-access-azure-portal.md)功能配置）
- 包含多个 SAML 终结点的应用可以在 Azure AD 中使用 PowerShell 进行配置（此功能在门户中不可用）
- 需要 SAML 1.1 版令牌的 WS 联合身份验证应用（例如 SharePoint 应用）必须使用 PowerShell 手动进行配置

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>目前在 Azure AD 中不受支持的应用/配置
目前无法迁移需要以下功能的应用。  如果你的应用需要这些功能，请在评论部分提供反馈。
- 协议功能
    - 支持对所有已登录应用执行 SAML 单一注销 (SLO) 操作
    - 支持 WS-Trust ActAs 模式
    - SAML 项目解析 
    - 对签名的 SAML 请求进行签名验证（注意，会接受签名的请求，但不会对签名进行验证）
 - 令牌功能 
     - SAML 令牌加密 
     - SAML 协议响应中的 SAML 1.1 版令牌 
- 令牌功能中的声明
    - 以声明的方式颁发本地组名称
    - 除 Azure AD 之外的存储中的声明
    - 复杂声明颁发转换规则（请参阅此[文档](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)和此[文档](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)，了解支持的声明映射）
    - 以声明方式颁发目录扩展
    - NameID 格式的自定义规范
    - 颁发多值属性

>[!NOTE]
>Azure AD 在不断发展，会在此领域添加更多的功能。 我们会定期更新此文档。 

### <a name="configuring-azure-ad"></a>配置 Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>配置 SaaS 应用的单一登录 (SSO) 设置

在 Azure AD 中，配置应用所要求的 SAML 登录是在应用的“单一登录属性”中的“用户属性”下完成的，如下所示：

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- 单击“查看和编辑其他所有用户属性”即可查看要在安全令牌中作为声明发送的属性

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- 单击特定的属性行即可进行编辑，也可单击“添加属性”来添加新属性。 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>将用户分配到应用
需要向 Azure AD 中的用户授予从 Azure AD 中进行访问的权限，这些用户才能登录到给定的 SaaS 应用。

若要在 Azure AD 门户中分配用户，请导航到 SaaS 应用在门户中的屏幕，然后单击侧栏中的“用户和组”。 若要添加用户或组，请单击屏幕顶部的“添加用户”。 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png) 用户应该在登录时在其[访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)中看到所述的 SaaS 应用（网址为 http://myapps.microsoft.com），这样才能验证访问权限。例如，已成功向用户分配 Salesforce 和 ServiceNow 的访问权限。

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>配置 SaaS 应用
能否从本地联合身份验证直接转换为 Azure AD 取决于所使用的 SaaS 应用是否支持多个标识提供者 (IdP)。  以下是一些有关多 IdP 支持的常见问题：

   **问：应用支持多个 IdP 意味着什么？**
    
   答：决定使用支持多个 IdP 的 SaaS 应用更改登录体验之前，可以先输入有关新 IdP（在我们的示例中为 Azure AD）的所有信息，  然后再在完成配置之后切换应用的身份验证配置，使之指向 Azure AD。

   问：为什么 SaaS 应用能否支持多个 IdP 很重要？

   答：如果不支持多个 IdP，则管理员必须留出一个短的时段（表现为服务性或维护性中断），在此时段内将 Azure AD 配置为应用的新 IdP。 在此中断期间，系统会向用户发出无法登录帐户的通知。

   如果应用不支持多个 IdP，可提前配置其他 IdP，这样管理员就可以在进行 Azure 直接转换时直接切换 IdP。

   另外，如果应用支持多个 IdP，而你选择使用多个 IdP 来同时处理登录时的身份验证，则用户可以在其登录页中选择用于身份验证的 IdP。

#### <a name="example-multiple-idp-support"></a>示例：多 IdP 支持
例如，在 Salesforce 中，IDP 配置位于“设置”->“公司设置”->“我的域”->“身份验证配置”下。

![](media/migrate-adfs-apps-to-azure/migrate9.png)

由于此前已在“标识”->“单一登录设置”下创建了配置，因此应该可以更改身份验证配置的 IdP，例如，从 AD FS 更改为 Azure AD。 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>可选：在 Azure AD 中配置用户预配
（可选）若要使用 Azure AD 来直接处理给定 SaaS 应用的用户预配，请参阅此文档，了解如何在 Azure AD 中管理企业应用的用户帐户预配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 管理应用程序](active-directory-enable-sso-scenario.md)
- [管理对应用的访问权限](active-directory-managing-access-to-apps.md)
- [Azure AD Connect 联合身份验证](active-directory-aadconnectfed-whatis.md)