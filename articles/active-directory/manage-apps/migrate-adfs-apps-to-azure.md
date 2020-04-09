---
title: 将应用程序身份验证从 AD FS 移动到 Azure 活动目录
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
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891866"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>将应用程序身份验证从活动目录联合服务移动到 Azure 活动目录

[Azure 活动目录 （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)提供了一个通用标识平台，可为人员、合作伙伴和客户提供单个标识，以便从任何平台和设备访问应用程序和协作。 Azure AD 具有[全套标识管理功能](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 标准化应用程序（应用）身份验证和授权到 Azure AD 可实现这些功能提供的好处。 

> [!NOTE]
> 本文重点介绍将应用程序身份验证从本地活动目录和活动目录联合身份验证服务移动到 Azure AD。 有关规划此移动的概述，请参阅将[应用程序身份验证迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper)的白皮书。 白皮书讨论了如何规划迁移、测试和见解。

## <a name="introduction"></a>简介

如果您有一个包含用户帐户的本地目录，则可能有许多用户对其进行身份验证的应用程序。 每个应用都配置为用户使用其身份进行访问。 


用户还可以直接使用本地活动目录进行身份验证。 活动目录联合服务 （AD FS） 是基于本地标识服务的标准。 AD FS 扩展了在受信任的业务合作伙伴之间使用单一登录 （SSO） 功能的能力，而无需用户单独登录到每个应用程序。 这称为"联合"。

许多组织具有软件即服务 （SaaS） 或自定义业务线 （LOB） 应用，这些应用与 AD FS 直接联合，以及基于 Office 365 和基于 Azure AD 的应用。 

![直接连接本地的应用程序](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**为了提高应用程序安全性，您的目标是在本地和云环境中拥有一组访问控件和策略**。 

![通过 Azure AD 连接的应用程序](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>要迁移的应用类型

将所有应用程序身份验证迁移到 Azure AD 是最佳选择，因为它为您提供了用于标识和访问管理的单个控制平面。

您的应用程序可能使用现代协议或旧协议进行身份验证。 请考虑首先迁移使用现代身份验证协议（如 SAML 和打开 ID 连接）的应用程序。 这些应用可以通过应用库中的内置连接器或通过在 Azure AD 中注册应用程序来重新配置为使用 Azure AD 进行身份验证。 使用较旧的协议的应用程序可以使用[应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)进行集成。 

有关详细信息，请参阅与[Azure AD 集成哪些类型的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)？

如果[启用了 Azure AD 连接运行状况](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)，则可以使用[AD FS 应用程序活动报表将应用程序迁移到 Azure AD。](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 

### <a name="the-migration-process"></a>迁移过程

在将应用身份验证移动到 Azure AD 的过程中，请充分测试应用和配置。 我们建议您继续使用现有的测试环境进行迁移到生产环境的迁移测试。 如果测试环境当前不可用，则可以使用[Azure 应用服务](https://azure.microsoft.com/services/app-service/)或[Azure 虚拟机](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)设置一个环境，具体取决于应用程序的体系结构。

您可以选择设置单独的测试 Azure AD 租户，以便在开发应用配置时使用。 

您的迁移过程可能如下所示：

**阶段 1 = 当前状态：使用 AD FS 进行生产应用身份验证**

![迁移阶段 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**阶段 2 = 可选：指向测试 Azure 租户的应用测试实例**

更新配置以将应用的测试实例指向测试 Azure AD 租户，并进行任何必要的更改。 应用可以与测试 Azure AD 租户中的用户一起进行测试。 在开发过程中，您可以使用[Fiddler](https://www.telerik.com/fiddler)等工具来比较和验证请求和响应。

如果设置单独的测试租户不可行，请跳过此阶段并启动应用的测试实例，并将其指向生产 Azure AD 租户，如下阶段 3 所述。

![迁移阶段 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**阶段 3 = 指向生产 Azure 租户的测试应用**

更新配置以将应用的测试实例指向 Azure 的生产实例。 您现在可以与生产实例中的用户一起进行测试。 如有必要，请查看本文中有关转换用户的部分。

![迁移阶段 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**阶段 4 = 指向生产 AD 租户的生产应用**

更新生产应用程序的配置以指向生产 Azure 租户。

![迁移阶段 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 使用 AD FS 进行身份验证的应用可能会使用活动目录组获得权限。 在开始迁移之前，使用[Azure AD 连接同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)在本地环境和 Azure AD 之间同步标识数据。 在迁移之前验证这些组和成员资格，以便在迁移应用程序时向同一用户授予访问权限。

### <a name="line-of-business-lob-apps"></a>业务线 （LOB） 应用

LOB 应用由您的组织在内部开发，或作为安装在数据中心的标准打包产品提供。 示例包括基于 Windows 标识基础和 SharePoint 应用构建的应用（不是在线共享点）。 

使用 OAuth 2.0、OpenID 连接或 WS-联合的 LOB 应用可以作为[应用注册](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)与 Azure AD 集成。 在[Azure 门户](https://portal.azure.com/)中的企业应用程序页上将使用 SAML 2.0 或 WS 联合作为[非库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)的自定义应用集成。

## <a name="saml-based-single-sign-on"></a>基于 SAML 的单登录

可以使用 SAML 2.0 进行身份验证的应用可以配置为[基于 SAML 的单登录](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)（基于 SAML 的 SSO）。 使用[基于 SAML 的 SSO，](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)您可以基于您在 SAML 声明中定义的规则将用户映射到特定的应用程序角色。 

要为基于 SAML 的单登录配置 SaaS 应用程序，请参阅[配置基于 SAML 的单登录](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)。 

![SSO SAML 用户屏幕截图 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
许多 SaaS 应用程序都有[特定于应用程序的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，该教程将逐步完成基于 SAML 的单登录的配置。

![应用程序教程](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些应用可以轻松地进行迁移。 具有较复杂要求（例如自定义声明）的应用可能需要在 Azure AD 和/或 Azure AD Connect 中进行其他配置。 有关受支持的声明映射的信息，请参阅[Azure 活动目录中的声明映射](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。

映射属性时请记住以下限制：

* 并非所有可以在 AD FS 中发出的属性都会在 Azure AD 中作为属性显示，以向 SAML 令牌发出，即使这些属性是同步的。 编辑属性时，"值"下拉列表将显示 Azure AD 中可用的不同属性。 检查[Azure AD 连接同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)配置，以确保所需的属性（例如 samAccountName）正在同步到 Azure AD。 可以使用扩展属性发出不属于 Azure AD 中标准用户架构的任何声明。

* 在最常用方案中，应用只需要 NameID 声明和其他常用的用户标识符声明。 要确定是否需要任何其他声明，请检查您从 AD FS 发出哪些声明。

* 并非所有声明都可能是问题，因为某些声明在 Azure AD 中受到保护。 

* 使用加密 SAML 令牌的能力现已处于预览状态。 请参阅[如何：自定义在企业应用程序的 SAML 令牌中发出的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

 

### <a name="software-as-a-service-saas-apps"></a>软件即服务 （SaaS） 应用

如果用户登录到 SaaS 应用（如 Salesforce、ServiceNow 或 Workday）并与 AD FS 集成，则您使用的是 SaaS 应用的联合登录。 

大多数 SaaS 应用程序已在 Azure AD 中配置。 Microsoft 在[Azure AD 应用库中](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)有许多与 SaaS 应用的预配置连接，这将使您的转换更容易。 SAML 2.0 应用程序可以通过 Azure AD 应用库或非[库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)与 Azure AD 集成。 

类似地，使用 OAuth 2.0 或 OpenID Connect 的应用可以以“应用注册”的方式与 Azure AD 集成。[](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) 使用旧协议的应用可以使用 Azure [AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)对 Azure AD 进行身份验证。

对于加入 SaaS 应用的任何问题，您可以联系[SaaS 应用程序集成支持别名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

**SSO 的 SAML 签名证书**：签名证书是任何 SSO 部署的重要组成部分。 Azure AD 创建签名证书，以建立基于 SAML 的联合 SSO 到 SaaS 应用程序。 添加库或非库应用程序后，将使用联合 SSO 选项配置添加的应用程序。 请参阅[在 Azure 活动目录中管理联合单一登录的证书](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>可移动的应用和配置

您目前可以轻松移动的应用包括使用标准配置元素和声明集的 SAML 2.0 应用：

* 用户主体名称

* 电子邮件地址

* 给定名称

* Surname

* 充当 SAML **NameID** 的备用属性，包括 Azure AD 邮件属性、邮件前缀、雇员 ID、扩展属性 1-15，或者本地 **SamAccountName** 属性。 有关详细信息，请参阅[编辑 NameIdentifier 声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

* 自定义声明。

以下需要其他配置步骤才能迁移到 Azure AD：

* AD FS 中的自定义授权或多重身份验证 （MFA） 规则。 您可以使用 Azure AD[条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)功能配置它们。

* 具有多个"答复 URL"终结点的应用。 通过使用 PowerShell 或在 Azure 门户界面中配置它们。

* 需要 SAML 1.1 版令牌的 WS 联合身份验证应用（例如 SharePoint 应用）。 您可以使用 PowerShell 手动配置它们。 您还可以为库中的 SharePoint 和 SAML 1.1 应用程序添加预集成的通用模板。 我们支持 SAML 2.0 协议。

* 复杂的声明颁发将转换规则。 有关支持的声明映射的信息，请参阅：
   *  [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [在 Azure Active Directory 中为企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>目前在 Azure AD 中不受支持的应用和配置

当前无法迁移需要以下功能的应用。

**协议功能**

* 支持 WS-信任 ActAs 模式

* SAML 项目解析

* 签名 SAML 请求的签名验证  
请注意，已签名的请求已接受，但签名未验证。  
鉴于 Azure AD 将仅将令牌返回到应用程序中预先配置的终结点，在大多数情况下可能不需要签名验证。

**令牌功能中的声明**

* 来自属性的声明存储 Azure AD 目录以外的其他声明，除非该数据同步到 Azure AD。 有关详细信息，请参阅 Azure [AD 同步 API 概述](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)。

* 发布目录多值属性。 例如，此时无法为代理地址发出多值声明。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>将应用设置从 AD FS 映射到 Azure AD

迁移开始时，会评估应用程序在本地的配置方式并将该配置映射到 Azure AD。 AD FS 和 Azure AD 的工作方式类似，因此配置信任、登录和注销 URL 以及标识符的概念在这两种情况下都适用。 记录应用程序的 AD FS 配置设置，以便在 Azure AD 中轻松配置它们。

### <a name="map-app-configuration-settings"></a>映射应用配置设置

下表描述了 AD FS 依赖方信任 Azure 企业应用程序之间的一些最常见的设置映射：

* AD FS – 在 AD FS 依赖方信任中查找应用的设置。 右键单击依赖方并选择"属性"。 

* Azure AD= 该设置在每个应用程序的单一登录属性中的[Azure 门户](https://portal.azure.com/)中配置。

| 配置设置| AD FS| 如何在 Azure AD 中配置| SAML 令牌 |
| - | - | - | - |
| **应用登录 URL** <p>用户登录服务提供商 （SP） 启动的 SAML 流中的应用的 URL。| 不可用| 从基于 SAML 的登录打开基本 SAML 配置| 不可用 |
| **应用回复 URL** <p>从标识提供程序 （IdP） 的角度进行应用的 URL。 用户登录到 IdP 后，IdP 会在此处将用户和令牌发送到此处。  这也称为**SAML 断言使用者终结点**。| 选择 **"端点"** 选项卡| 从基于 SAML 的登录打开基本 SAML 配置| SAML 令牌中的目标元素。 示例值：[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **应用注销 URL** <p>这是当用户从应用注销时向其发送"注销清理"请求的 URL。 IdP 也发送从所有其他应用注销用户的请求。| 选择 **"端点"** 选项卡| 从基于 SAML 的登录打开基本 SAML 配置| 不可用 |
| **应用标识符** <p>从 IdP 的角度来看，这是应用标识符。 登录 URL 值通常用于标识符（但并非始终如此）。  有时，应用将此称为"实体 ID"。| 选择 **"标识符"** 选项卡|从基于 SAML 的登录打开基本 SAML 配置| 映射到 SAML 令牌中的 **"访问群体**"元素。 |
| **应用联合元数据** <p>这是应用联合元数据的位置。 IdP 用它来自动更新特定的配置设置，例如终结点或加密证书。| 选择"**监视"** 选项卡| 不适用。 Azure AD 不支持直接使用应用程序联合元数据。 您可以手动导入联合元数据。| 不可用 |
| **用户标识符/名称 ID** <p>一个属性，用于以唯一方式向应用指示 Azure AD 或 AD FS 中的用户标识。  此属性通常是 UPN 或用户的电子邮件地址。| 声明规则。 在大多数情况下，声明规则发出以 Name标识符结尾的类型的声明。| 您可以在标题 **"用户属性"和"声明"** 下找到标识符。 默认情况下，使用 UPN| 映射到 SAML 令牌中的**NameID**元素。 |
| **其他索赔** <p>通常从 IdP 发送到应用的其他声明信息的示例包括名字、姓氏、电子邮件地址和组成员身份。| 在 AD FS 中，可以看到此项充当信赖方的其他声明规则。| 您可以在标题下找到标识符 **，用户属性&声明**。 选择“查看”，然后编辑所有其他的用户属性。****| 不可用 |


### <a name="map-identity-provider-idp-settings"></a>映射标识提供程序 （IdP） 设置

将应用程序配置为指向 SSO 的 Azure AD 和 AD FS。 在这里，我们重点关注使用 SAML 协议的 SaaS 应用。 但是，此概念也适用于自定义 LOB 应用。

> [!NOTE]
> Azure AD 的配置值遵循 Azure 租户 ID 替换 [租户 id] 和应用程序 ID 替换 [应用程序 id] 的模式。 您可以在 Azure 活动目录>属性下的[Azure 门户](https://portal.azure.com/)中找到此信息： 

* 选择目录 ID 以查看租户 ID。 

* 选择应用程序 ID 以查看应用程序 ID。

 在高级别上，将以下关键 SaaS 应用配置元素映射到 Azure AD。 

 

| 元素| 配置值 |
| - | - |
| 身份提供者颁发者| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| 身份提供商登录 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 标识提供程序注销 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 联合元数据位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>为 SaaS 应用映射 SSO 设置

SaaS 应用需要知道向何处发送身份验证请求以及如何验证收到的令牌。 下表描述了在应用中配置 SSO 设置的元素，以及它们在 AD FS 和 Azure AD 中的值或位置

| 配置设置| AD FS| 如何在 Azure AD 中配置 |
| - | - | - |
| **IdP 登录 URL** <p>从应用的角度登录 IdP 的登录 URL（用户被重定向以登录）。| AD FS 登录 URL 是 AD FS 联合服务名称，后跟"/adfs/ls/"。 <p>例如：[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| 将 [租户 ID] 替换为租户 ID。 <p> 对于使用 SAML-P 协议的应用：[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>对于使用 WS-联合协议的应用：[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 注销 URL**<p>从应用的角度注销 IdP 的注销 URL（当用户选择注销应用时，用户被重定向）。| 注销 URL 与登录 URL 相同，或者与附加"wa_wsignout1.0"的 URL 相同。 例如：[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| 将 [租户 ID] 替换为租户 ID。<p>对于使用 SAML-P 协议的应用：<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> 对于使用 WS-联合协议的应用：[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **令牌签名证书**<p>IdP 使用证书的私钥对已颁发的令牌进行签名。 它可以验证令牌是否来自已配置应用所信任的 IdP。| AD FS 令牌签名证书位于 AD FS 管理中的“证书”下。****| 在标题**SAML 签名证书**下的应用程序的**单一登录属性**中的 Azure 门户中找到它。 可以在其中下载要上传到应用的证书。  <p>如果应用程序有多个证书，则可以在联合元数据 XML 文件中找到所有证书。 |
| **标识符/"颁发者"**<p>从应用的角度来看 IdP 的标识符（有时称为"颁发者 ID"）。<p>在 SAML 令牌中，该值显示为颁发者元素。| AD FS 的标识符通常是在 **"编辑联合服务属性"** 下，AD FS 管理中的联合服务标识符>。 例如：[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| 将 [租户 ID] 替换为租户 ID。<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **IdP 联合元数据**<p>IdP 公开可用的联合元数据的位置。 （某些应用使用联合元数据来分别代替管理员配置 URL、标识符、令牌签名证书。）| 在"**服务>终结点>元数据>类型：联合元数据**"下，在 AD FS 管理中查找 AD FS 联合元数据 URL。 例如：[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Azure AD 的相应值遵循模式[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)。 将 [租户域名] 替换为租户的名称，格式为"contoso.onmicrosoft.com"。   <p>有关详细信息，请参阅[联合元数据](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>在 Azure AD 中表示 AD FS 安全策略

将应用身份验证移动到 Azure AD 时，请从现有安全策略创建映射到 Azure AD 中可用的等效或替代变体。 确保在满足应用所有者要求的安全标准的同时可以完成这些映射，将使应用的其余部分迁移更加轻松。

对于每种规则类型及其示例，我们在此处建议该规则在 AD FS、AD FS 规则语言等效代码中的外观，以及此映射在 Azure AD 中的方式。

### <a name="map-authorization-rules"></a>映射授权规则

以下是 AD FS 中授权规则类型的示例，以及如何将它们映射到 Azure AD：

#### <a name="example-1-permit-access-to-all-users"></a>示例 1：允许所有用户访问

允许访问所有用户类似于 AD FS： 

![迁移阶段 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


这将以以下方式之一映射到 Azure AD：

在[Azure 门户](https://portal.azure.com/)中 ：
* 选项 1：将所需的用户分配设置为"否" ![编辑 SaaS 应用的访问控制策略 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    请注意，将"用户分配"设置为"是"要求将用户分配到应用程序以获得访问权限。 当设置为"否"时，所有用户都有权访问。 此开关不控制"我的应用"体验中针对用户的显示内容。 

 
* 选项 2：在"用户和组"选项卡中，将应用程序分配给"所有用户"自动组。 <p>
您必须在 Azure AD 租户中[启用动态组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)，默认的"所有用户"组才能可用。

   ![Azure AD 中的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>示例 2：显式允许组

AD FS 中的显式组授权：


![颁发授权规则 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


这是规则映射到 Azure AD 的方式：

在[Azure 门户](https://portal.azure.com/)中，将首先创建对应于 AD FS 中的用户组[的用户组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)，然后将应用权限分配给该组：

![添加分配 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>示例 3：授权特定用户

AD FS 中的显式用户授权：

![颁发授权规则 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

这是规则映射到 Azure AD 的方式：

在[Azure 门户](https://portal.azure.com/)中，通过应用的"添加分配"选项卡将用户添加到应用，如下所示：

![Azure 中的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>映射多重身份验证规则 

[多因素身份验证 （MFA）](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)和 AD FS 的本地部署在迁移后仍将工作，因为您与 AD FS 联合。 但是，请考虑迁移到绑定到 Azure AD 的条件访问工作流的 Azure 内置 MFA 功能。 

以下是 AD FS 中 MFA 规则类型的示例，以及如何根据不同的条件将它们映射到 Azure AD：

AD FS 中的 MFA 规则设置：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>示例 1：根据用户/组强制实施 MFA

用户/组选择器是一个规则，允许您在每个组（组 SID）或每个用户（主要 SID）的基础上强制实施 MFA。 除了用户/组分配之外，AD FS MFA 配置 UI 中的所有附加复选框将作为在强制执行用户/组规则后计算的其他规则。 


为 Azure AD 中的用户或组指定 MFA 规则：

1. 创建新[的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 选择**分配**。 添加要强制实施 MFA 的用户或组。

3. 配置 **"访问"控件**选项，如下所示：  
‎

![AAD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>示例 2：对未注册的设备强制实施 MFA

为 Azure AD 中未注册的设备指定 MFA 规则：

1. 创建新[的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 将 **"分配给****所有用户**"

3. 配置 **"访问"控件**选项，如下所示：  
‎

![AAD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
当您将"For 多个控件"选项设置为"需要其中一个选定的控件"时，这意味着如果用户满足该复选框指定的任何条件，则这些控件将被授予对应用的访问权限。

#### <a name="example-3-enforce-mfa-based-on-location"></a>示例 3：根据位置强制实施 MFA

根据用户在 Azure AD 中的位置指定 MFA 规则：

1. 创建新[的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

1. 将 **"分配给****所有用户**"

1. [在 Azure AD 中配置命名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)，否则信任从公司网络内部进行联合。 

1. 配置**条件规则**以指定要为其强制实施 MFA 的位置。

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 配置 **"访问"控件**选项，如下所示：


![映射访问控制策略](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>将 Emit 属性映射为声明规则

下面是如何在 AD FS 中映射属性的示例：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


这是规则映射到 Azure AD 的方式：

在[Azure 门户](https://portal.azure.com/)中，选择**企业应用程序**、**单一登录**和添加**SAML 令牌属性**，如下所示：

![Azure AD MFA 设置](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>映射内置访问控制策略

AD FS 2016 具有多种内置访问控制策略，您可以选择：

![内置访问控制的 Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
要在 Azure AD 中实现内置策略，可以使用[新的条件访问策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)并配置访问控件，也可以使用 AD FS 2016 中的自定义策略设计器配置访问控制策略。 规则编辑器有一个详尽的许可证和例外选项的列表，可帮助您进行各种排列。

![Azure AD 访问控制策略](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



在此表中，我们列出了一些有用的"允许"和"例外"选项，以及它们如何映射到 Azure AD。 


| | 如何在 Azure AD 中配置"允许"选项？| 如何在 Azure AD 中配置"除"选项？ |
| - | - | - |
| 从特定网络| 映射到 Azure AD 中的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)| 对[受信任的位置](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)使用 **"排除**"选项 |
| 来自特定组| [设置用户/组分配](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 在用户和组中使用 **"排除**"选项 |
| 来自具有特定信任级别的设备| 在分配 ->条件下的"设备状态"控件设置| 在"设备状态条件"下使用 **"排除**"选项，并包括**所有设备** |
| 请求中的特定声明| 无法迁移此设置| 无法迁移此设置 |


如何为 Azure 门户中的受信任位置配置"排除"选项的示例：

![映射访问控制策略的屏幕截图](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>将用户从 AD FS 转换为 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>在 Azure AD 中同步 AD FS 组

映射授权规则时，使用 AD FS 进行身份验证的应用可能会使用 Active Directory 组来授予权限。 在这种情况下，在迁移应用程序之前，使用[Azure AD 连接](https://go.microsoft.com/fwlink/?LinkId=615771)将这些组与 Azure AD 同步。 请确保在迁移之前验证这些组和成员资格，以便在迁移应用程序时向同一用户授予访问权限。

有关详细信息，请参阅[使用从活动目录 同步的组属性的先决条件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)。

### <a name="setup-user-self-provisioning"></a>设置用户自预配 

某些 SaaS 应用程序支持用户首次登录应用程序时自行预配功能。 在 Azure 活动目录 （Azure AD） 中，术语应用预配是指在用户需要访问的云[（SaaS](https://azure.microsoft.com/overview/what-is-saas/)） 应用程序中自动创建用户标识和角色。 迁移的用户已在 SaaS 应用程序中具有帐户。 迁移后添加的任何新用户都需要预配。 迁移应用程序后测试[SaaS 应用预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)。

### <a name="sync-external-users-in-azure-ad"></a>在 Azure AD 中同步外部用户

您的现有外部用户可能在 AD FS 中以两种主要方式进行设置：

#### <a name="external-users-with-a-local-account-within-your-organization"></a>在组织内具有本地帐户的外部用户

您将继续能够以与内部用户帐户相同的方式使用这些帐户。 这些外部用户帐户在组织内具有原则名称，尽管该帐户的电子邮件可能指向外部。 在迁移中，可以通过迁移这些用户以在此类标识可用时使用自己的公司标识来利用[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)提供的优势。 这简化了这些用户的登录过程，因为他们通常使用自己的公司登录登录。 由于不再需要管理外部用户的帐户，组织的管理也将得到缓解。

#### <a name="federated-external-identities"></a>联合外部标识

如果您当前正在与外部组织联合，则有几种方法可以采用：

* [在 Azure 门户中添加 Azure 活动目录 B2B 协作用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。 您可以主动将 B2B 协作邀请从 Azure AD 管理门户发送到合作伙伴组织，以便各个成员继续使用他们习惯的应用和资产。 

* [创建自助服务 B2B 注册工作流](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)，该工作流使用 B2B 邀请 API 为合作伙伴组织的个人用户生成请求。

无论现有外部用户的配置方式如何，他们都可能具有与其帐户关联的权限，包括组成员身份或特定权限。 评估是否需要迁移或清理这些权限。 在用户迁移到外部标识后，需要禁用组织中表示外部用户的帐户。 迁移过程应与您的业务合作伙伴讨论，因为他们连接到资源的能力可能会中断。

## <a name="migrate-and-test-your-apps"></a>迁移和测试应用

请遵循本文中详述的迁移过程。

然后转到[Azure 门户](https://aad.portal.azure.com/)以测试迁移是否成功。 请根据以下说明进行操作：
1. 选择**企业应用程序** > **所有应用程序**，并从列表中找到你的应用。

1. 选择 **"管理** > **用户和组**"以至少向应用分配一个用户或组。

1. 选择 **"管理** > **条件访问**"。 查看策略列表，并确保不会使用[条件访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)阻止对应用程序的访问。

根据配置应用的方式，验证 SSO 是否正常工作。 

| 身份验证类型| 正在测试 |
| - | - |
| OAuth / 开 ID 连接| 选择**企业应用程序>权限**，并确保您已同意在应用的用户设置中使用在您的组织中使用的应用程序。  
‎ |
| 基于 SAML 的 SSO| 使用 **"单一登录**"下的["测试 SAML 设置"](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues)按钮。  
‎ |
| 基于密码的 SSO| 下载并安装[MyApps 安全登录](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[扩展](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)。 此扩展可帮助您启动组织的任何需要您使用 SSO 流程的云应用。  
‎ |
| 应用程序代理| 确保连接器正在运行并分配给应用程序。 请访问[应用程序代理故障排除指南](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)以获得进一步帮助。  
‎ |

> [!NOTE]
> 来自旧 AD FS 环境的 Cookie 仍将在用户的计算机上持久。 这些 Cookie 可能会导致迁移问题，因为用户可能会定向到旧的 AD FS 登录环境，而不是新的 Azure AD 登录。 您可能需要手动清除用户浏览器 Cookie 或使用脚本。 您还可以使用系统中心配置管理器或类似平台。

### <a name="troubleshoot"></a>疑难解答

如果迁移的应用程序测试有任何错误，则故障排除可能是回到现有 AD FS 依赖方之前的第一步。 请参阅[如何调试 Azure 活动目录中的应用程序基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)。

### <a name="rollback-migration"></a>回滚迁移

如果迁移失败，我们建议您将现有的依赖方留在 AD FS 服务器上，并取消对依赖方的访问。 这将允许在部署期间根据需要快速回退。

### <a name="end-user-communication"></a>最终用户通信

虽然计划的中断窗口本身可能很小，但您仍应计划主动向员工传达这些时间帧，同时从 AD FS 到 Azure AD 进行截止。 确保应用体验具有"反馈"按钮，或指向帮助台解决问题的指针。

部署完成后，您可以发送通信，通知用户成功部署，并提醒用户他们需要执行的任何新步骤。

* 指示用户使用[访问面板](https://myapps.microsoft.com.com/)访问所有迁移的应用程序。 

* 提醒用户可能需要更新其 MFA 设置。 

* 如果部署了自助服务密码重置，用户可能需要更新或验证其身份验证方法。 请参阅[MFA](https://aka.ms/mfatemplates)和[SSPR](https://aka.ms/ssprtemplates)最终用户通信模板。

与外部用户的通信：在出现问题时，这组用户通常是受影响最严重的。 如果安全状态为外部合作伙伴规定了一组不同的条件访问规则或风险配置文件，则尤其如此。 确保外部合作伙伴了解云迁移计划，并有一个时间范围，鼓励他们参与测试外部协作独有的所有流的试验部署。 最后，确保他们有一种方法访问您的帮助台，以防出现问题。

## <a name="next-steps"></a>后续步骤
读取[将应用程序身份验证迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
设置[条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)和[MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
