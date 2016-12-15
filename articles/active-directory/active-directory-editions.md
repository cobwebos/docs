---
title: "Azure Active Directory 版本 | Microsoft 文档"
description: "本文介绍 Azure Active Directory 的免费版和付费版选项。 Azure Active Directory Basic、Azure Active Directory Premium P1 和 Azure Active Directory Premium P2 是付费版本。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: dcaf8939-7633-40a8-bd76-27dedbb6083a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b241d70ab7c7e8468a3f358fef5af47a89d42e56


---
# <a name="azure-active-directory-editions"></a>Azure Active Directory 版本
所有 Microsoft Online 业务服务都依赖于 Azure Active Directory (Azure AD) 进行登录及满足其他标识需求。 如果订阅了任何 Microsoft Online 业务服务（例如，Office 365 或 Microsoft Azure），则表示已获得 Azure AD，可用于访问下述所有免费功能。  

Azure Active Directory 是一个综合性的、高度可用的标识和访问管理云解决方案，它将核心目录服务、高级标识监管和应用程序访问管理结合起来。 Azure Active Directory 还提供了功能丰富的基于标准的平台，该平台支持开发人员根据集中的策略和规则为应用程序提供访问控制。 使用 Azure Active Directory 免费版，你可以管理用户和组、与本地目录同步，以及在 Azure、Office 365 和数千种主流 SaaS 应用程序（如 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等）上单一登录。 若要了解有关 Azure Active Directory 的详细信息，请阅读[什么是 Azure AD？](active-directory-whatis.md)

若要增强 Azure Active Directory，可以使用 Azure Active Directory Basic、Premium P1、和 Premium P2 版添加付费功能。 Azure Active Directory 付费版建立在现有免费目录基础之上，提供企业级功能，包括自助服务、增强型监视、安全报告、Multi-Factor Authentication (MFA) 和移动工作者安全访问。

Office 365 订阅包括以下比较表中所述的其他 Azure Active Directory 功能。

> [!NOTE]
> 有关这些版本的定价选项，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。 中国地区目前不支持 Azure Active Directory Premium P1、Premium P2 和 Azure Active Directory Basic。 有关详细信息，请通过 Azure Active Directory 论坛与我们联系。
>
>

* **Azure Active Directory Basic** - 面向具有云优先需求的任务工作者，此版本提供以云为中心的应用程序访问和自助标识管理解决方案。 使用 Azure Active Directory 基本版，你可以增强工作效率并获得成本缩减功能，例如基于组的访问管理、用于云应用程序的自助密码重置、Azure Active Directory 应用程序代理（使用 Azure Active Directory 发布本地 Web 应用程序），以及 99.9％ 正常运行时间的企业级 SLA。
* **Azure Active Directory Premium P1** - Azure Active Directory Premium 版添加了丰富的企业级标识管理功能，并允许各类用户无缝访问本地与云功能，旨在满足组织更加严苛的标识和访问管理需求。 此版本为混合环境中的信息工作者和标识管理员提供一切必要的功能，让他们执行应用程序访问、自助标识和访问管理 (IAM)、标识保护及实现云中的安全性。 它支持动态组和自助服务组管理等高级管理与委派资源。 它包含 Microsoft 标识管理器（一个本地标识与访问管理套件），并提供云写回功能，使本地用户能够使用自助密码重置等解决方案。
* **Azure Active Directory Premium P2** - 这款新产品包含 Azure AD Premium P1，以及新的 Identity Protection 和 Privileged Identity Management 中的所有功能，旨在为所有用户和管理员提供高级保护。 Azure Active Directory Identity Protection 利用数十亿信号，针对应用程序和重要公司数据提供基于风险的条件访问。 此外，我们还使用 Azure Active Directory Privileged Identity Management 帮助管理和保护特权帐户，方便发现、限制和监视管理员以及他们对资源的访问，并在需要时提供实时访问。  

若要立即注册并开始使用 Active Directory Premium，请参阅 [Azure Active Directory Premium 入门](active-directory-get-started-premium.md)。

> [!NOTE]
> 许多 Azure Active Directory 功能以“即付即用”版本的形式提供：
>
> * Active Directory B2C 是适用于面向消费者应用程序的标识和访问管理解决方案。 有关更多详细信息，请参阅 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * 可以通过基于用户或基于身份验证的提供程序使用 Azure Multi-Factor Authentication。 有关更多详细信息，请参阅[什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)
>
>

## <a name="comparing-generally-available-features"></a>比较正式推出的功能
> [!NOTE]
> 有关此数据的不同视图，请参阅 [Azure Active Directory 功能](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx)。
>
>

**常用功能**

* [目录对象](#directory-objects)
* [用户/组管理（添加/更新/删除）/基于用户的预配、设备注册](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
* [单一登录 (SSO)](#single-sign-on-sso)
* [云用户的自助密码更改](#self-service-password-change-for-cloud-users)
* [Connect（将本地目录扩展到 Azure Active Directory 的同步引擎）](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)
* [安全性/使用情况报告](#securityusage-reports)

**Basic 功能**

* [基于组的访问管理/预配](#group-based-access-managementprovisioning)
* [云用户的自助密码重置](#self-service-password-reset-for-cloud-users)
* [公司品牌（登录页/访问面板自定义）](#company-branding-logon-pagesaccess-panel-customization)
* [应用程序代理](#application-proxy)
* [SLA 99.9%](#sla-999)

**Premium P1 功能**

* [自助组和应用管理/自助应用程序添加件/动态组](#self-service-group)
* [通过本地写回实现自助密码重置/更改/解锁](#self-service-password-resetchangeunlock-with-on-premises-write-back)
* [多重身份验证（云和本地（MFA 服务器））](#multi-factor-authentication-cloud-and-on-premises-mfa-server)
* [MIM CAL + MIM 服务器](#mim-cal-mim-server)
* [Cloud App Discovery](#cloud-app-discovery)
* [Connect Health](#connect-health)
* [组帐户的自动密码滚动更新](#automatic-password-rollover-for-group-accounts)

**Premium P2 功能**

* [Identity Protection](active-directory-identityprotection.md)
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

**Azure Active Directory Join – 仅适用于 Windows 10 的相关功能**

* [让设备加入 Azure AD、Desktop SSO、Microsoft Passport for Azure AD 和 Administrator Bitlocker 恢复](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)
* [MDM 自动注册、自助 Bitlocker 恢复、通过 Azure AD Join 将其他本地管理员加入 Windows 10 设备](#mdm-auto-enrollment)

## <a name="common-features"></a>常用功能
#### <a name="directory-objects"></a>目录对象
**类型：**常用功能

默认使用配额为 150,000 个对象。 对象是目录服务中的一个实体，由其唯一可分辨名称表示。 对象的一个例子就是用于身份验证目的的用户条目。 如果你需要超过此默认使用量配额，请与支持人员联系。 50 万个对象限制不适用于 Office 365、Microsoft Intune 或任何其他依赖 Azure Active Directory 提供目录服务的 Microsoft 付费联机服务。

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| 最多 500,000 个对象 |无对象限制 |无对象限制 |Office 365 用户帐户没有对象限制 |

#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device-registration"></a>用户/组管理（添加/更新/删除）, 基于用户的预配、设备注册
**类型：**常用功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| ![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |

**更多详细信息：**

* [管理 Azure AD 目录](active-directory-administer.md)
* [Azure Active Directory 设备注册概述](active-directory-conditional-access-device-registration-overview.md)

#### <a name="single-sign-on-sso"></a>单一登录 (SSO)
**类型：**常用功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| 每个用户 10 个应用 (1) |每个用户 10 个应用 (1) |无限制 (2) |每个用户 10 个应用 (1) |

1. 使用 Azure AD 免费版和 Azure AD 基本版，最终用户可以通过单一登录访问多达 10 个应用程序。
2. 使用应用程序库菜单中提供的模板，自助集成支持 SAML、SCIM 或基于窗体的身份验证的任何应用程序。 有关更多详细信息，请参阅[针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](active-directory-saas-custom-apps.md)。

**更多详细信息：**

* [使用 Azure Active Directory (AD) 管理应用程序](active-directory-enable-sso-scenario.md)

#### <a name="self-service-password-change-for-cloud-users"></a>云用户的自助密码更改
**类型：**常用功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| ![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |

**更多详细信息：**

* [如何更新自己的密码](active-directory-passwords-update-your-own-password.md)

#### <a name="connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Connect（将本地目录扩展到 Azure Active Directory 的同步引擎）
**类型：**常用功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| ![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |

**更多详细信息：**

* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

#### <a name="securityusage-reports"></a>安全/使用情况报告
**类型：**常用功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| 3 个基本报告 |3 个基本报告 |高级报告 |3 个基本报告 |

**更多详细信息：**

* [查看访问和使用情况报告](active-directory-view-access-usage-reports.md)

## <a name="premium-and-basic-features"></a>Premium 和 Basic 功能
#### <a name="group-based-access-managementprovisioning"></a>基于组的访问管理/预配
**类型：**Basic 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾选标记][12] | ![勾选标记][12] | &nbsp; |

**更多详细信息：**

* [使用组管理对 SaaS 应用程序的访问](active-directory-accessmanagement-group-saasapps.md)

#### <a name="self-service-password-reset-for-cloud-users"></a>云用户的自助密码重置
**类型：**Basic 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾选标记][12] |![勾选标记][12] | ![勾选标记][12] |

**更多详细信息：**

* [用户和管理员的 Azure AD 密码重置](active-directory-passwords.md)

#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>公司品牌（登录页/访问面板自定义）
**类型：**Basic 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾选标记][12] |![勾选标记][12] | ![勾选标记][12] |

**更多详细信息：**

* [向“登录”和“访问面板”页添加公司品牌](active-directory-add-company-branding.md)

#### <a name="application-proxy"></a>应用程序代理
**类型：**Basic 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾选标记][12] | ![勾选标记][12] | &nbsp; |

**更多详细信息：**

* [如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)

#### <a name="sla-999"></a>SLA 99.9%
**类型：**Basic 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾选标记][12] |![勾选标记][12] | ![勾选标记][12] |

**更多详细信息：**

* [服务级别协议](https://azure.microsoft.com/support/legal/sla/)

## <a name="premium-features"></a>Premium 功能


#### <a name="a-nameself-service-groupaself-service-group-and-app-managementself-service-application-additionsdynamic-groups"></a><a name="self-service-group"></a>自助组和应用管理/自助应用程序添加/动态组
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12]| &nbsp; |

#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>通过本地写回实现自助密码重置/更改/解锁
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Multi-Factor Authentication（云和本地（MFA 服务器））
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![勾选标记][12] |对于 Office 365 应用限制为云 |

**更多详细信息：**

* [什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)


#### <a name="a-namemim-cal-mim-serveramim-cal-mim-server"></a><a name="mim-cal-mim-server"></a>MIM CAL + MIM 服务器
Microsoft 标识管理器服务器软件权限随 Windows Server 许可证（任意版本）一起授予。 由于 Microsoft 标识管理器在 Windows Server 操作系统上运行，只要服务器正在运行有效的、经过许可的 Windows Server 副本，就能在该服务器上安装和使用 Microsoft 标识管理器。 Microsoft 标识管理器服务器不需要其他单独的许可证。

**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![勾选标记][12] | &nbsp; |

#### <a name="cloud-app-discovery"></a>Cloud App Discovery
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

**更多详细信息：**

* [使用 Cloud App Discovery 查找非托管的云应用程序](active-directory-cloudappdiscovery-whatis.md)

#### <a name="azure-ad-connect-health"></a>Azure AD Connect Health
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

**更多详细信息：**

* [在云中监视本地标识基础结构和同步服务](active-directory-aadconnect-health.md)

#### <a name="automatic-password-rollover-for-group-accounts"></a>组帐户的自动密码滚动更新
**类型：**Premium 功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

#### <a name="identity-protection"></a>标识保护
**类型：**Premium 功能

| 免费版 | 基本版 | Premium P2 版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

#### <a name="privileged-identity-management"></a>Privileged Identity Management
**类型：**Premium 功能

| 免费版 | 基本版 | Premium P2 版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

## <a name="azure-active-directory-join-windows-10-only-related-features"></a>Azure Active Directory Join – 仅适用于 Windows 10 的相关功能
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>让设备加入 Azure AD、Desktop SSO、Microsoft Passport for Azure AD 和 Administrator Bitlocker 恢复
**类型：**Azure Active Directory Join - 仅适用于 Windows 10 的相关功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| ![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |![勾选标记][12] |


#### <a name="a-namemdm-auto-enrollmentamdm-auto-enrollment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join"></a><a name="mdm-auto-enrollment"></a>MDM 自动注册、自助 Bitlocker 恢复、通过 Azure AD Join 将其他本地管理员加入 Windows 10 设备
**类型：**Azure Active Directory Join - 仅适用于 Windows 10 的相关功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

#### <a name="enterprise-state-roaming"></a>企业状态漫游
**类型：**Azure Active Directory Join - 仅适用于 Windows 10 的相关功能

**可用性：**

| 免费版 | 基本版 | Premium（P1 和 P2）版 | 仅限 Office 365 应用 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾选标记][12] | &nbsp; |

**更多详细信息：**

* [企业状态漫游](active-directory-windows-enterprise-state-roaming-overview.md)

## <a name="azure-ad-preview-features"></a>Azure AD 预览功能
除 Free、Basic 和 Premium（P1 和 P2）版正式推出的功能外，Azure AD 还提供一系列预览功能。 你可以通过预览功能了解不久的将来可用的功能，并判断这些功能能否帮助改善你的环境。

**可用的预览功能：**

* [B2B 协作](active-directory-b2b-collaboration-overview.md)
* [管理单元](active-directory-administrative-units-management.md)
* [HR 应用程序集成](active-directory-saas-workday-inbound-tutorial.md)
* [iOS 上基于证书的身份验证](active-directory-certificate-based-authentication-ios.md)
* [Android 上基于证书的身份验证](active-directory-certificate-based-authentication-android.md)

## <a name="whats-next"></a>后续步骤
* [Azure Active Directory 高级版入门](active-directory-get-started-premium.md)
* [向“登录”和“访问面板”页添加公司品牌](active-directory-add-company-branding.md)
* [查看访问和使用情况报告](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png



<!--HONumber=Dec16_HO2-->


