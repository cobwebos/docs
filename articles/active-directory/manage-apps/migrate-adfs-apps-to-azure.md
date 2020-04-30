---
title: 将应用程序身份验证从 AD FS 移动到 Azure Active Directory
description: 本文旨在帮助组织了解如何将应用程序移到 Azure AD，并着重介绍联合 SaaS 应用程序。
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30b777cce9b704be558460edf20cf243258c160b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82202292"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>将应用程序身份验证从 Active Directory 联合身份验证服务移动到 Azure Active Directory

[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)提供一个通用标识平台，为你的用户、合作伙伴和客户提供单一标识，用于访问应用程序以及从任何平台和设备进行协作。 Azure AD 具有一整套[标识管理功能](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 通过标准化应用程序（应用）身份验证和授权来 Azure AD 可实现这些功能所提供的好处。 

> [!NOTE]
> 本文重点介绍如何将应用程序身份验证从本地 Active Directory 和 Active Directory 联合身份验证服务迁移到 Azure AD。 有关规划此迁移的概述，请参阅将[应用程序身份验证迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper)白皮书。 本白皮书介绍了如何规划迁移、测试和见解。

## <a name="introduction"></a>简介

如果你的本地目录包含用户帐户，则你可能有许多用户对其进行身份验证的应用程序。 其中每个应用都配置为用户使用其标识进行访问。 


用户还可以直接通过本地 Active Directory 进行身份验证。 Active Directory 联合身份验证服务（AD FS）是基于标准的本地标识服务。 AD FS 扩展了在可信业务合作伙伴之间使用单一登录（SSO）功能的能力，而无需用户单独登录到每个应用程序。 这称为 "联合"。

许多组织的软件即服务（SaaS）或自定义业务线（LOB）应用直接与 Office 365 和基于 Azure AD 的应用进行了联合 AD FS。 

![直接连接到本地的应用程序](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**为了提高应用程序的安全性，你的目标是在本地和云环境中拥有一组访问控制和策略**。 

![通过 Azure AD 连接的应用程序](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>要迁移的应用类型

将所有应用程序身份验证迁移到 Azure AD 是最佳的，因为它提供了一个用于标识和访问管理的控制平面。

您的应用程序可能使用新式或旧的协议进行身份验证。 首先，请考虑迁移使用新式身份验证协议（如 SAML 和 Open ID Connect）的应用程序。 可以重新配置这些应用，以便通过应用库中的内置连接器或在 Azure AD 中注册应用程序，对 Azure AD 进行身份验证。 使用较旧协议的应用可以使用[应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)进行集成。 

有关详细信息，请参阅[可与 Azure AD 集成哪些类型的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)？

如果[启用了 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)，则可以使用[AD FS 应用程序活动报表将应用程序迁移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 。 

### <a name="the-migration-process"></a>迁移过程

在将应用程序身份验证移动到 Azure AD 的过程中，充分测试应用和配置。 建议你继续使用现有的测试环境，将迁移测试转移到生产环境。 如果测试环境当前不可用，则可以使用[Azure App Service](https://azure.microsoft.com/services/app-service/)或[Azure 虚拟机](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)进行设置，具体取决于应用程序的体系结构。

你可以选择设置单独的测试 Azure AD 租户，以在开发应用配置时使用。 

迁移过程可能如下所示：

**阶段 1-当前状态：用 AD FS 进行身份验证的生产应用**

![迁移阶段1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**阶段2–可选：指向测试 Azure 租户的应用的测试实例**

更新配置，将应用程序的测试实例指向测试 Azure AD 租户，并进行任何所需的更改。 应用可以通过测试 Azure AD 租户中的用户进行测试。 在开发过程中，可以使用[Fiddler](https://www.telerik.com/fiddler)等工具来比较和验证请求和响应。

如果设置单独的测试租户并不可行，请跳过此阶段并创建应用的测试实例，并将其指向生产 Azure AD 租户，如以下第3阶段所述。

![迁移阶段2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**阶段 3-测试应用指向生产 Azure 租户**

更新配置，将应用程序的测试实例指向 Azure 的生产实例。 你现在可以在生产实例中与用户进行测试。 如有必要，请查看本文中有关转换用户的部分。

![迁移阶段3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**阶段 4-指向生产 AD 租户的生产应用**

更新生产应用程序的配置以指向生产 Azure 租户。

![迁移阶段1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 使用 AD FS 进行身份验证的应用可能会使用 Active Directory 组进行权限。 在开始迁移之前，请使用[Azure AD Connect 同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)来同步本地环境和 Azure AD 之间的标识数据。 在迁移之前验证这些组和成员身份，以便你可以在迁移应用程序时向相同用户授予访问权限。

### <a name="line-of-business-lob-apps"></a>业务线（LOB）应用

LOB 应用由组织内部开发，或者作为你的数据中心内安装的标准打包产品提供。 示例包括基于 Windows Identity Foundation 和 SharePoint 应用（而不是 SharePoint Online）构建的应用。 

使用 OAuth 2.0、OpenID Connect 或 WS 联合身份验证的 LOB 应用程序可与 Azure AD 作为[应用注册](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)进行集成。 将使用 SAML 2.0 或 WS 联合身份验证的自定义应用作为[Azure 门户](https://portal.azure.com/)中 "企业应用程序" 页上的[非库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)进行集成。

## <a name="saml-based-single-sign-on"></a>基于 SAML 的单一登录

可以为[基于 saml 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)（基于 SAML 的 SSO）配置使用 saml 2.0 进行身份验证的应用。 对于[基于 saml 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)，可以根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色。 

若要为基于 SAML 的单一登录配置 SaaS 应用程序，请参阅[配置基于 saml 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)。 

![SSO SAML 用户屏幕截图 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
许多 SaaS 应用程序都有一个[特定于应用程序的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，该教程逐步指导你完成基于 SAML 的单一登录的配置。

![应用教程](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些应用可以轻松地进行迁移。 具有较复杂要求（例如自定义声明）的应用可能需要在 Azure AD 和/或 Azure AD Connect 中进行其他配置。 有关支持的声明映射的详细信息，请参阅[中的声明映射 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。

映射属性时，请记住以下限制：

* 并非可以在 AD FS 中颁发的所有属性都将显示在 Azure AD 中，作为要发出到 SAML 令牌的属性，即使这些属性已同步。 在编辑属性时，"值" 下拉列表将显示 Azure AD 中可用的不同属性。 检查[Azure AD Connect 同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)配置，确保将所需属性（例如 samAccountName）同步到 Azure AD。 你可以使用扩展属性在 Azure AD 中发出不属于标准用户架构的任何声明。

* 在最常用方案中，应用只需要 NameID 声明和其他常用的用户标识符声明。 若要确定是否需要其他声明，请检查 AD FS 颁发的声明。

* 并非所有声明都可以解决，因为某些声明在 Azure AD 中受到保护。 

* 使用加密 SAML 令牌的功能现在处于预览阶段。 请参阅[如何：为企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

 

### <a name="software-as-a-service-saas-apps"></a>软件即服务（SaaS）应用

如果用户登录到 SaaS 应用（例如 Salesforce、ServiceNow 或 Workday），并与 AD FS 集成，则使用的是适用于 SaaS 应用的联合登录。 

大多数 SaaS 应用程序都可以在 Azure AD 中进行配置。 Microsoft 在[Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中提供了许多与 SaaS 应用的预配置连接，这将使你的过渡更容易。 SAML 2.0 应用程序可以通过 Azure AD 应用库或[非库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)与 Azure AD 集成。 

类似地，使用 OAuth 2.0 或 OpenID Connect 的应用可以以“应用注册”的方式与 Azure AD 集成。[](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) 使用旧版协议的应用可以使用[Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)来向 Azure AD 进行身份验证。

有关载入 SaaS 应用的任何问题，可联系[Saas 应用程序集成支持别名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

**Sso 的 SAML 签名证书**：签名证书是任何 SSO 部署的重要部分。 Azure AD 创建签名证书，以便将基于 SAML 的联合 SSO 建立到 SaaS 应用程序。 添加库或非库应用程序后，将使用联合 SSO 选项配置添加的应用程序。 请参阅[Azure Active Directory 中的 "管理用于联合单一登录的证书](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)"。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>当今可移动的应用和配置

目前可以轻松迁移的应用包括使用一组标准配置元素和声明的 SAML 2.0 应用：

* 用户主体名称

* 电子邮件地址

* 给定名称

* Surname

* 充当 SAML **NameID** 的备用属性，包括 Azure AD 邮件属性、邮件前缀、雇员 ID、扩展属性 1-15，或者本地 **SamAccountName** 属性。 有关详细信息，请参阅[编辑 NameIdentifier 声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

* 自定义声明。

下面需要执行其他配置步骤来迁移到 Azure AD：

* AD FS 中的自定义授权或多重身份验证（MFA）规则。 您可以使用 " [Azure AD 条件性访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)" 功能对其进行配置。

* 具有多个回复 URL 终结点的应用。 你可以使用 PowerShell 或 Azure 门户接口在 Azure AD 中配置它们。

* 需要 SAML 1.1 版令牌的 WS 联合身份验证应用（例如 SharePoint 应用）。 你可以使用 PowerShell 手动对其进行配置。 还可以从库中添加 SharePoint 和 SAML 1.1 应用程序的预先集成的通用模板。 我们支持 SAML 2.0 协议。

* 复杂声明颁发转换规则。 有关支持的声明映射的信息，请参阅：
   *  [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [在 Azure Active Directory 中为企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>目前在 Azure AD 中不受支持的应用和配置

目前不能迁移需要以下功能的应用。

**协议功能**

* 支持 WS-TRUST ActAs 模式

* SAML 项目解析

* 签名的 SAML 请求的签名验证  
请注意，会接受签名的请求，但不会对签名进行验证。  
假定 Azure AD 仅将令牌返回到应用程序中预先配置的终结点，则在大多数情况下可能不需要签名验证。

**令牌功能中的声明**

* 除 Azure AD 目录之外的属性存储中的声明，除非该数据已同步到 Azure AD。 有关详细信息，请参阅[Azure AD 同步 API 概述](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)。

* Directory 多值属性的颁发。 例如，我们此时无法为代理地址颁发多值声明。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>将应用设置从 AD FS 映射到 Azure AD

迁移开始时，会评估应用程序在本地的配置方式并将该配置映射到 Azure AD。 AD FS 和 Azure AD 的工作方式类似，因此配置信任、登录和注销 Url 以及标识符的概念在这两种情况下都适用。 记录应用程序的 AD FS 配置设置，以便可以轻松地在 Azure AD 中配置这些设置。

### <a name="map-app-configuration-settings"></a>映射应用程序配置设置

下表描述了 AD FS 信赖方信任到 Azure AD 企业应用程序之间的一些最常见设置映射：

* AD FS –查找应用的 AD FS 信赖方信任中的设置。 右键单击信赖方，然后选择 "属性"。 

* Azure AD –设置在每个应用程序的单一登录属性[Azure 门户](https://portal.azure.com/)内配置。

| 配置设置| AD FS| 如何在 Azure AD 中配置| SAML 令牌 |
| - | - | - | - |
| **应用登录 URL** <p>用于登录到服务提供商（SP）启动的 SAML 流中的应用的用户的 URL。| 空值| 从基于 SAML 的登录打开基本 SAML 配置| 空值 |
| **应用回复 URL** <p>从标识提供者（IdP）的角度来看应用程序的 URL。 用户登录到 IdP 后，IdP 会将用户和令牌发送到此处。  这也称为**SAML 断言使用者终结点**。| 选择 "**终结点**" 选项卡| 从基于 SAML 的登录打开基本 SAML 配置| SAML 令牌中的 Destination 元素。 示例值： `https://contoso.my.salesforce.com` |
| **应用注销 URL** <p>这是用户从应用程序中注销时，向其发送 "注销清理" 请求的 URL。 IdP 发送请求，同时从其他所有应用注销用户。| 选择 "**终结点**" 选项卡| 从基于 SAML 的登录打开基本 SAML 配置| 空值 |
| **应用标识符** <p>这是 IdP 的透视中的应用标识符。 登录 URL 值通常用于标识符（但并非始终）。  有时，应用程序会将 "实体 ID" 称为 "实体 ID"。| 选择 "**标识符**" 选项卡|从基于 SAML 的登录打开基本 SAML 配置| 映射到 SAML 令牌中的**受众**元素。 |
| **应用联合元数据** <p>这是应用的联合元数据的位置。 IdP 用它来自动更新特定的配置设置，例如终结点或加密证书。| 选择 "**监视**" 选项卡| 不适用。 Azure AD 不支持直接使用应用程序联合元数据。 您可以手动导入联合元数据。| 空值 |
| **用户标识符/名称 ID** <p>一个属性，用于以唯一方式向应用指示 Azure AD 或 AD FS 中的用户标识。  此属性通常为用户的 UPN 或电子邮件地址。| 声明规则。 在大多数情况下，声明规则使用以 NameIdentifier 结尾的类型发出声明。| 可以在标头**用户属性和声明**下找到标识符。 默认情况下，将使用 UPN| 映射到 SAML 令牌中的**NameID**元素。 |
| **其他声明** <p>通常从 IdP 发送到应用的其他声明信息的示例包括名字、姓氏、电子邮件地址和组成员身份。| 在 AD FS 中，可以看到此项充当信赖方的其他声明规则。| 可以在标头用户属性下找到标识符 **& 声明**。 选择“查看”，然后编辑所有其他的用户属性。****| 空值 |


### <a name="map-identity-provider-idp-settings"></a>地图标识提供者（IdP）设置

将应用程序配置为指向 SSO Azure AD 与 AD FS。 这里，我们将重点介绍使用 SAML 协议的 SaaS 应用。 不过，此概念还适用于自定义 LOB 应用。

> [!NOTE]
> Azure AD 的配置值遵循 Azure 租户 ID 替换 {租户 id} 的模式，应用程序 ID 替换 {Application-id}。 可在 Azure Active Directory > 属性下的[Azure 门户](https://portal.azure.com/)中找到此信息： 

* 选择 "目录 ID" 查看租户 ID。 

* 选择 "应用程序 ID" 以查看你的应用程序 ID。

 在高级别中，将以下关键 SaaS 应用配置元素映射到 Azure AD。 

 

| 元素| 配置值 |
| - | - |
| 标识提供者颁发者| https：\//sts.windows.net/{tenant-id}/ |
| 标识提供者登录 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 标识提供者注销 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 联合元数据位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>映射 SaaS 应用的 SSO 设置

SaaS 应用需要知道将身份验证请求发送到何处，以及如何验证收到的令牌。 下表介绍了在应用程序中配置 SSO 设置的元素，以及它们在 AD FS 和 Azure AD 中的值或位置

| 配置设置| AD FS| 如何在 Azure AD 中配置 |
| - | - | - |
| **IdP 登录 URL** <p>从应用的角度来看，IdP 的登录 URL （将用户重定向以登录）。| AD FS 登录 URL 是 AD FS 的联合身份验证服务名称，后跟 "/adfs/ls/." <p>例如：`https://fs.contoso.com/adfs/ls/`| 将 {租户 id} 替换为你的租户 ID。 <p> 对于使用 SAML-P 协议的应用：[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>对于使用 WS 联合身份验证协议的应用：[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 注销 URL**<p>从应用的角度注销 IdP 的 URL （当用户选择从应用中注销时，用户会重定向）。| 注销 URL 既可以是登录 URL，也可以是附加了 "wa = wsignout1.0 1.0" 的同一 URL。 例如：`https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| 将 {租户 id} 替换为你的租户 ID。<p>对于使用 SAML-P 协议的应用：<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> 对于使用 WS 联合身份验证协议的应用：[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **令牌签名证书**<p>IdP 使用证书的私钥对颁发的令牌进行签名。 它可以验证令牌是否来自已配置应用所信任的 IdP。| AD FS 令牌签名证书位于 AD FS 管理中的“证书”下。****| 在应用程序的 "**单一登录属性**" 中的 " **SAML 签名证书**" 下，查找 Azure 门户。 可以在其中下载要上传到应用的证书。  <p>如果应用程序有多个证书，则可以在联合元数据 XML 文件中找到所有证书。 |
| **标识符/"issuer"**<p>从应用的角度来看，IdP 的标识符（有时称为 "颁发者 ID"）。<p>在 SAML 令牌中，此值显示为 Issuer 元素。| AD FS 的标识符通常是 AD FS 管理中的联合身份验证服务标识符， **> 编辑联合身份验证服务属性**。 例如：`http://fs.contoso.com/adfs/services/trust`| 将 {租户 id} 替换为你的租户 ID。<p>https：\//sts.windows.net/{tenant-id}/ |
| **IdP 联合元数据**<p>IdP 的公开可用联合元数据的位置。 （某些应用使用联合元数据来分别代替管理员配置 URL、标识符、令牌签名证书。）| 在 AD FS 管理中查找管理 > 下的 AD FS 联合元数据 URL **> 元数据 > 类型：联合元数据**。 例如：`https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD 的相应值遵循模式[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)。 将 {TenantDomainName} 替换为 "contoso.onmicrosoft.com" 格式的租户名称。   <p>有关详细信息，请参阅[联合元数据](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>表示 Azure AD AD FS 安全策略

在将应用程序身份验证移动到 Azure AD 时，请创建从现有安全策略到它们在 Azure AD 中可用的等效或备用变量的映射。 确保在满足应用程序所有者要求的安全标准时可以完成这些映射，这会使应用程序迁移的其余部分更加容易。

对于每种规则类型及其示例，我们建议在此说明规则在 AD FS、AD FS 规则语言等效代码以及该映射在 Azure AD 中的样子。

### <a name="map-authorization-rules"></a>映射授权规则

下面是 AD FS 中的授权规则类型的示例，以及如何将它们映射到 Azure AD：

#### <a name="example-1-permit-access-to-all-users"></a>示例1：允许所有用户访问

允许访问所有用户的 AD FS 如下所示： 

![迁移阶段1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


这将通过以下方式之一映射到 Azure AD：

在[Azure 门户](https://portal.azure.com/)中：
* 选项1：将用户分配设置为 "否" ![编辑 SaaS 应用的访问控制策略 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    请注意，将 "需要进行用户分配" 切换到 "是" 要求将用户分配到应用程序才能获得访问权限。 如果设置为 "否"，则所有用户都具有访问权限。 此开关不会控制用户在 "我的应用" 体验中的显示内容。 

 
* 选项2：在 "用户和组" 选项卡中，将应用程序分配给 "所有用户" 自动组。 <p>
必须在 Azure AD 租户中[启用动态组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)，才能使用默认的 "所有用户" 组。

   ![Azure AD 中的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>示例2：显式允许组

AD FS 中的显式组授权：


![颁发授权规则 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


此规则将映射到 Azure AD：

在[Azure 门户](https://portal.azure.com/)中，你将首先[创建一个](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)与 AD FS 中的用户组相对应的用户组，然后将应用权限分配给该组：

![添加分配 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>示例3：向特定用户授权

AD FS 中的显式用户授权：

![颁发授权规则 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

此规则将映射到 Azure AD：

在[Azure 门户](https://portal.azure.com/)中，通过应用的 "添加分配" 选项卡将用户添加到应用，如下所示：

![Azure 中的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>映射多重身份验证规则 

迁移后，[多重身份验证（MFA）](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)和 AD FS 的本地部署仍将起作用，因为你要与 AD FS 联合。 不过，请考虑迁移到 Azure 的内置 MFA 功能，这些功能与 Azure AD 的条件访问工作流相关联。 

下面是 AD FS 中的 MFA 规则类型的示例，以及如何根据不同条件将它们映射到 Azure AD：

AD FS 中的 MFA 规则设置：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>示例1：基于用户/组强制执行 MFA

用户/组选择器是一项规则，该规则允许你根据每个组（组 SID）或每个用户（主 SID）强制实施 MFA。 除了用户/组分配以外，AD FS MFA 配置 UI 中的所有其他复选框都可以作为在强制执行用户/组规则后评估的其他规则。 


在 Azure AD 中指定用户或组的 MFA 规则：

1. 创建[新的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 选择“分配”  。 添加要对其强制执行 MFA 的用户或组。

3. 配置**Access** control 选项，如下所示：  
‎

![AAD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>示例2：对未注册的设备强制实施 MFA

为 Azure AD 中未注册的设备指定 MFA 规则：

1. 创建[新的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 将**分配**设置为 "**所有用户**"。

3. 配置**Access** control 选项，如下所示：  
‎

![AAD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
如果将 "对于多个控件" 选项设置为 "需要一个选定的控件"，这意味着，如果用户满足此复选框指定的任何一个条件，则会向用户授予对应用的访问权限。

#### <a name="example-3-enforce-mfa-based-on-location"></a>示例3：根据位置强制执行 MFA

基于用户在 Azure AD 中的位置指定 MFA 规则：

1. 创建[新的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

1. 将**分配**设置为 "**所有用户**"。

1. [配置中的命名位置 Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)否则来自企业网络内部的联合身份验证受信任。 

1. 配置**条件规则**以指定要对其强制实施 MFA 的位置。

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 配置**Access** control 选项，如下所示：


![映射访问控制策略](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>将发出属性作为声明规则

下面是如何在 AD FS 中映射属性的示例：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


此规则将映射到 Azure AD：

在[Azure 门户](https://portal.azure.com/)中，选择 "**企业应用程序**"、"**单一登录**" 和 "添加**SAML 令牌属性**"，如下所示：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>映射内置的访问控制策略

AD FS 2016 具有几个内置的访问控制策略，你可以从中进行选择：

![Azure AD 内置的访问控制](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
若要在 Azure AD 中实现内置策略，你可以使用[新的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)并配置访问控制，或者可以使用 AD FS 2016 中的自定义策略设计器来配置访问控制策略。 规则编辑器包含允许和选项的详尽列表，可帮助您进行各种排列。

![Azure AD 访问控制策略](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



在此表中，我们列出了一些有用的允许和除外选项，以及它们如何映射到 Azure AD。 


| | 如何在 Azure AD 中配置允许选项？| 如何在 Azure AD 中配置 Except 选项？ |
| - | - | - |
| 从特定网络| 映射到 Azure AD 中的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)| 将**Exclude**选项用于[受信任位置](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| 从特定组| [设置用户/组分配](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 在用户和组中使用 "**排除**" 选项 |
| 从具有特定信任级别的设备| 从 "作业" 下的 "设备状态" 控件中设置此项-> 条件| 在 "设备状态条件" 下使用 "**排除**" 选项并包括 "**所有设备**" |
| 具有请求中的特定声明| 无法迁移此设置| 无法迁移此设置 |


有关如何在 Azure 门户中配置受信任位置的排除选项的示例：

![映射访问控制策略的屏幕截图](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>将用户从 AD FS 过渡到 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>同步 Azure AD 中的 AD FS 组

映射授权规则时，使用 AD FS 进行身份验证的应用可能会使用 Active Directory 组进行权限。 在这种情况下，在迁移应用程序之前，请使用[Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)将这些组与 Azure AD 同步。 请确保在迁移之前验证这些组和成员身份，以便你可以在迁移应用程序时向相同用户授予访问权限。

有关详细信息，请参阅[使用从 Active Directory 同步的组属性的先决条件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)。

### <a name="setup-user-self-provisioning"></a>设置用户自助预配 

一些 SaaS 应用程序支持在用户首次登录应用程序时自行预配用户。 在 Azure Active Directory （Azure AD）中，术语 "应用设置" 是指在用户需要访问的云（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）应用程序中自动创建用户标识和角色。 已迁移的用户在 SaaS 应用程序中已有一个帐户。 需要预配迁移后添加的任何新用户。 在应用程序迁移后测试[SaaS 应用配置](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)。

### <a name="sync-external-users-in-azure-ad"></a>同步 Azure AD 中的外部用户

可以通过两种主要方式在 AD FS 中设置现有的外部用户：

#### <a name="external-users-with-a-local-account-within-your-organization"></a>组织中具有本地帐户的外部用户

你将继续以内部用户帐户的工作方式使用这些帐户。 尽管该帐户的电子邮件可能指向外部，但这些外部用户帐户在你的组织中具有主体名称。 在迁移过程中，你可以通过将这些用户迁移到使用自己的公司标识（当此类标识可用时）来利用[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)提供的好处。 这简化了这些用户的登录过程，因为他们通常是通过他们自己的公司登录名登录的。 你的组织的管理将减轻，不再需要管理外部用户的帐户。

#### <a name="federated-external-identities"></a>联合外部标识

如果你当前正在与外部组织联合，则可以使用以下几种方法：

* [将 AZURE ACTIVE DIRECTORY B2B 协作用户添加到 Azure 门户中](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。 你可以主动将 B2B 协作邀请从 Azure AD 管理门户发送到各个成员的合作伙伴组织，以继续使用他们所用的应用和资产。 

* 使用 B2B 邀请 API[创建一个自助服务 B2B 注册工作流，该工作流](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)使用 B2B 邀请 API 为你的合作伙伴组织中的单个用户生成请求。

无论现有的外部用户是如何配置的，它们都有权与其帐户关联的权限，无论是在组成员身份还是特定权限中。 评估这些权限是否需要迁移或清理。 在用户迁移到外部标识后，组织中表示外部用户的帐户需要禁用。 应将迁移过程与业务合作伙伴进行讨论，因为它们可能会中断连接到资源的能力。

## <a name="migrate-and-test-your-apps"></a>迁移和测试应用

请按照本文中详细说明的迁移过程进行操作。

然后，请前往[Azure 门户](https://aad.portal.azure.com/)以测试迁移是否成功。 请根据以下说明进行操作：
1. 从列表中选择 "**企业应用程序** > " "**所有应用程序**" 和 "查找应用"。

1. 选择 "**管理** > **用户和组**"，将至少一个用户或组分配到应用。

1. 选择 "**管理** > **条件性访问**"。 查看策略列表，并确保不会使用[条件性访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)阻止对应用程序的访问。

根据你配置应用的方式，验证 SSO 是否正常工作。 

| 身份验证类型| 测试 |
| - | - |
| OAuth/OpenID Connect| 选择 "**企业应用程序" > 权限**，并确保你同意在你的组织中的应用程序的用户设置中使用该应用程序。  
‎ |
| 基于 SAML 的 SSO| 使用 "**单一登录**" 下的 "[测试 SAML 设置](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues)" 按钮。  
‎ |
| 基于密码的 SSO| 下载并安装[MyApps 安全登录](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[扩展](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)。 此扩展可帮助你启动组织的任何需要使用 SSO 过程的云应用。  
‎ |
| 应用程序代理| 确保连接器正在运行并已分配给应用程序。 请访问[应用程序代理故障排除指南](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)以获取进一步的帮助。  
‎ |

> [!NOTE]
> 旧 AD FS 环境中的 cookie 在用户计算机上仍然是永久性的。 这些 cookie 可能会导致迁移出现问题，因为用户可能会定向到旧的 AD FS 登录环境与新的 Azure AD 登录名。 可能需要手动或使用脚本来清除用户浏览器 cookie。 你还可以使用 System Center Configuration Manager 或类似的平台。

### <a name="troubleshoot"></a>疑难解答

如果在测试已迁移的应用程序时出现任何错误，则在回退到现有 AD FS 信赖方之前，排除故障可能是第一步。 请参阅[如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)。

### <a name="rollback-migration"></a>回滚迁移

如果迁移失败，我们建议你将现有信赖方留在 AD FS 服务器上，并删除对信赖方的访问权限。 如果需要，可以在部署期间进行快速回退。

### <a name="end-user-communication"></a>最终用户通信

尽管计划内的停机时间可能很小，但你仍应计划在从 AD FS 到 Azure AD 时，将这些时间表主动传达给员工。 确保你的应用程序体验有一个反馈按钮，或指向你的支持人员的指向问题的指针。

部署完成后，可以发送通信通知用户成功部署，并提醒他们需要执行的任何新步骤。

* 指示用户使用[访问面板](https://myapps.microsoft.com)访问所有已迁移的应用程序。 

* 提醒用户他们可能需要更新其 MFA 设置。 

* 如果部署了自助密码重置，则用户可能需要更新或验证其身份验证方法。 请参阅[MFA](https://aka.ms/mfatemplates)和[SSPR](https://aka.ms/ssprtemplates)最终用户通信模板。

与外部用户的通信：这组用户在出现问题时通常是最严重的影响。 如果安全状况为外部合作伙伴提供一组不同的条件性访问规则或风险配置文件，则更是如此。 确保外部合作伙伴了解云迁移计划，并在时间范围内鼓励他们参与试验部署，以测试外部协作独有的所有流。 最后，确保在出现重大问题时，它们可以访问你的支持人员。

## <a name="next-steps"></a>后续步骤
读取[将应用程序身份验证迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
设置[条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)和[MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
