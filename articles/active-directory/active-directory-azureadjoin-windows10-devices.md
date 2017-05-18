---
title: "在工作区中使用 Windows 10 设备 | Microsoft 文档"
description: "向用户和 IT 管理员提供各种功能的快照，用于比较企业中使用 Windows 10 可以设置和使用设备的不同方法。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 94ccc8fd-b17b-4fda-8d56-9d87aa37a9f9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 71e8944fc56420ddd7fd42bbfbfdf6d145a1cf3b
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017


---
# <a name="using-windows-10-devices-in-your-workplace"></a>在工作区中使用 Windows 10 设备
适用于：Windows 10 电脑

Windows 10 提供了三种适用于组织的模型，使用户能够以安全又便捷的方式访问。

* **Azure Active Directory Join** (Azure AD Join)：适合访问主要位于云中的资源（如 Office 365）的员工。 Azure AD Join 是 Windows 10 中新增的自助式工作设置体验。
* **域加入**：适合投资于本地应用和资源的组织。 在与 Azure AD 相连的情况下，Domain join 可以改善 Windows 10 中的体验。
* **一个新的简化版 BYOD 体验**：适合想要将工作帐户或学校帐户添加到 Windows，然后轻松地访问个人设备上的资源的用户。

下表向用户和 IT 管理员简要介绍了各种功能，比较在企业中通过 Windows 10 设置和使用设备的不同方法。

|  | 域加入 | Azure AD Join | 个人设备 |
| --- | --- | --- | --- |
| 适用于工作帐户或学校帐户的 Windows 设备登录。 |是 |是 |否 |
| 用户单一登录 (SSO) 到 Office 365 和 Azure AD 应用。 SSO 提供只需登录一次就可以访问组织资源的功能。 |是 |是 |是 |
| 用户 SSO 到 Kerberos/NTLM 应用。 |是 |受限制 |是，通过 VPN |
| 工作帐户或学校帐户与 Microsoft Passport 和 Windows Hello 结合使用，实现强授权和方便登录。 |是 |是 |是 |
| 使用工作帐户或学校帐户（非 Microsoft 帐户）访问企业版 Windows 应用商店。 |是 |是 |是 |
| 以符合企业规范的方式在使用工作帐户或学校帐户的设备间漫游用户设置。 |是 |是 |是 |
| 可以将组织的应用限制为仅符合组织设备策略的设备可以访问。 |是 |是 |是 |
| 通过用户自助式设备预配“在任何地点工作”。 |否 |是 |是 |
| 可以管理设备。 |是，通过 GP/SCCM |是 |是 |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>在 Windows 10 中，将工作专用设备与 Azure AD Join 和域加入结合使用
在 Windows 10 中，工作专用设备可以通过两种方法来访问工作资源：

* Azure AD Join
* 域加入
  
  这两种方法都可以是有效选项，具体取决于组织的需求和要求。 在某些情况下，组织可能会受益于同时启用这两种部署方法。

## <a name="when-to-use-azure-active-directory-join"></a>何时使用 Azure Active Directory Join
Azure AD Join 是 Windows 10 中新增的自助式工作预配体验。  适合访问主要位于云中的工作资源（如 Office 365）的员工。 它是一种用于为企业配置计算机、平板电脑和手机的简便方法。 通过移动设备管理在各 Windows 平台间使用一致的控制，对设备进行管理。

**出于以下任一原因使用 Azure AD Join**：

* 想要支持经常外出的员工对设备进行自助式预配。
* 提供给用户诸如平板电脑和手机之类的工作专用移动设备。
* 想要在 Azure AD（而非 Active Directory）中管理一组用户，如临时工、承包商或学生。
* 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。
* 没有本地 Active Directory。

某些组织会将 Azure AD Join 用作部署工作专用设备的主要方法，尤其是当他们将其资源的大部分或全部迁移到云中时。 同时使用 Active Directory 和 Azure AD 的混合型组织还可以选择部署一种方法或另一种，具体取决于用户或部门。

例如，学区和大学可能会在 Active Directory 中管理教工，在 Azure AD 中管理学生。 一些公司可能想要在 Azure AD 中管理远程办公室或销售部门。 Azure AD Join 和域加入方法在混合型组织内都可以使用。 对于在工作环境中部署设备而言，Azure AD Join 可以是域加入的极好补充。

**在通常通过云访问企业资源的情况下，如果做到以下几点，组织可能进一步受益**：

* 能够消除对本地标识基础设施的依赖。
* 能够通过允许自助式配置简化设备部署模型，摆脱映像解决方案。
* 能够使用移动设备管理在不同平台上管理所有设备。

有关 Azure AD Join 的详细信息，请参阅[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)。

## <a name="when-to-use-domain-join-or-keep-using-it"></a>何时使用域加入（或持续使用它）
过去 15 年，许多组织一直在使用域加入连接工作设备。 它支持用户使用其 Active Directory 工作帐户或学校帐户登录他们的设备。 域加入还允许 IT 部门对这些设备进行集中式全面管理。 组织通常依靠映像方法预配设备，并常常使用 System Center Configuration Manager (SCCM) 或组策略 (GP) 管理这些设备。

**出于以下任一原因，企业应使用域加入（或持续使用它）**：

* 已将 Win32 应用部署到使用 NTLM/Kerberos 的设备。
* 需要使用 GP 或 SCCM/DCM 管理设备。
* 想要继续使用映像解决方案为员工配置设备。

**在与 Azure AD 相连的情况下，Windows 10 中的域加入还带来以下好处**：

* 通过 Microsoft Passport 和 Windows Hello 为工作帐户或学校帐户实现强设备绑定身份验证并使其登录更加便捷。
* 使用工作帐户或学校帐户（不需要 Microsoft 帐户）的设备可以访问企业版 Windows 应用商店。
* 以符合企业规范的方式在使用工作帐户或学校帐户（不需要 Microsoft 帐户）的设备间漫游用户设置。
* 可以将组织的应用限制为仅符合组织设备策略的设备可以访问。

有关 Azure AD Join 的详细信息，请参阅[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)。

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>支持为工作或学校加入个人所有设备
为了在企业中支持 BYOD，Windows 10 使用户可以将工作帐户或学校帐户添加到其计算机、平板电脑或手机。 在用户完成添加工作帐户或学校帐户后，该设备会注册到 Azure AD，并且可选择在组织已完成配置的移动设备管理系统中进行注册。 这些设备会在该目录中显示为“已注册”与“已加入 Azure AD”。 IT 管理员可以基于该信息应用不同的策略，以便根据需要针对个人所有设备执行较宽松的控制（相较于工作专用设备）。

用户可以非常方便地将工作帐户或学校帐户添加到其个人设备。 用户可以在首次访问工作应用程序时执行此操作，也可以通过“设置”菜单手动执行该操作。 该帐户将可对组织的资源执行 SSO。

有关 Azure AD Join 的详细信息，请参阅[体验 Windows 10 时将已加入域的设备连接到 Azure AD](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="enable-domain-join-or-azure-ad-join"></a>启用域加入或 Azure AD Join
前面所述的所有方法（域加入、Azure AD Join 和添加工作帐户或学校帐户）在 Windows 10 用户体验中都有入口点。 不过，在可以进行体验之前，所有方法都需要 IT 管理员在基础结构中启用该功能。

## <a name="requirements-for-deploying-azure-ad-join"></a>部署 Azure AD Join 的要求
若要为任一组用户部署 Azure AD Join，需要满足以下条件：

* 一个 Azure AD 订阅。
* 在需要更多功能的情况下，一个 Azure AD Premium 订阅（如移动设备管理自动注册）。
* 移动设备管理 - 例如，一个 Microsoft Intune 订阅、适用于 Office 365 的移动设备管理或与 Azure AD 集成的任一合作伙伴移动设备管理供应商。 （有关详细信息，请参阅本文临近结尾的[常见问题部分](#frequently-asked-questions)）。

如果你的设施为混合型，我们强烈建议你部署 Azure AD Connect 将本地目录扩展到 Azure AD。

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>结合 Azure AD 使用域加入的要求
域加入像往常一样继续工作。 不过，若要通过 Azure AD 获得好处，需要满足以下条件：

* 一个 Azure AD 订阅。
* 部署 Azure AD Connect 将本地目录扩展到 Azure AD。
* 允许已加入域的设备有条件访问 Azure AD 的策略。
* 当希望能够限制某些设备的访问时，允许访问“已加入域的”设备的策略。
* 启用要求兼容设备的规则的 System Center Configuration Manager 版本 1509 技术预览版。 （请参阅 TechNet 文档和博客文章）。

有关 Windows 10 中的域加入的详细信息，请参阅[体验 Windows 10 时将已加入域的设备连接到 Azure AD](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>使用 BYOD 和“添加工作帐户或学校帐户”的要求
若要启用工作帐户或学校帐户的“自带设备办公”(BYOD)，需要满足以下条件：

* 一个 Azure AD 订阅。
* 在需要更多功能的情况下，一个 Azure AD Premium 订阅（如移动设备管理自动注册）。

## <a name="requirements-for-using-microsoft-passport"></a>使用 Microsoft Passport 的要求
若要启用 Microsoft Passport，需要满足以下条件：

* 基于证书的身份验证支持（使用 Microsoft Passport）的公钥基础结构 (PKI)。
* 基于证书的身份验证支持（将 Microsoft Passport 用于 Azure AD Join 和工作帐户或学校帐户）的 Intune 订阅。
* 基于证书的身份验证支持（将 Microsoft Passport 用于域加入）的 System Center Configuration Manager 版本 1509 技术预览版（请参阅 TechNet 文档和博客文章）。
* 在组织中启用 Microsoft Passport 的策略。

作为使用 PKI 的替代方法，可以通过执行以下操作启用基于密钥的 Microsoft Passport：

* 部署 Windows Server 2016“生产预览 1”DC（无需针对域或林功能级别；多个 DC 实现每个 Active Directory 站点足够的冗余服务）。
* 设置在组织中启用 Microsoft Passport 的策略。

有关 Windows 10 中 Microsoft Passport 和 Windows Hello 的详细信息，请参阅 <link-to-MS-Passport-and-Windows-Hello-document>。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>哪一个合作伙伴的移动设备管理产品与 Azure AD 集成？
在 Windows 10 中，以下供应商的产品与 Azure AD 集成实现统一的注册和条件访问：

* AirWatch by VMware
* Citrix Xenmobile
* Lightspeed Mobile Manager
* SOTI 本地移动设备管理
* MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Windows 10 中的工作区加入有何作用？
在 Windows 8.1 中使用工作区加入是为了支持 BYOD。 在 Windows 10 中，通过“添加工作帐户或学校帐户”支持 BYOD，如本文档中前面所述。 对于未将其移动设备管理与 Azure AD 集成的组织，用户可以通过以下途径将设备手动注册到管理：“设置” > “帐户” > “工作访问”。

### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>在 Windows 10 中，用户是否可以将其 Microsoft 帐户连接到其域帐户？
在 Windows 10 中不行。 在 Windows 8.1 中，已加入域的设备的用户可以将其 Microsoft 帐户（例如，Hotmail、Live、Outlook、Xbox 等）“连接”到其域帐户，启用某些体验（诸如 SSO 到 Live 服务、使用 Windows 应用商店和在设备间漫游用户设置）。 在 Windows 10 中，Microsoft 帐户的“连接”功能已停用。 用户可以将一个或多个 Microsoft 帐户添加为其他帐户，以便支持 SSO 到诸如 Windows 应用商店等使用者服务。 这通过在“设置” > “帐户” > “你的帐户”中实现。

正在从已加入域的 Windows 8.1 设备进行升级和已连接其 Microsoft 帐户的用户会自动将用户已连接的 Microsoft 帐户添加到用户所用的其他帐户列表。

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)


