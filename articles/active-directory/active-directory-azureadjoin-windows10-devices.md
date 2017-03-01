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
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 053c04ed222eb9c9203a25153e45e31ba842b223


---
# <a name="using-windows-10-devices-in-your-workplace"></a>在工作区中使用 Windows 10 设备
适用于：Windows 10 电脑

Windows 10 提供了三种适用于组织的模型，使用户能够以安全又便捷的方式访问工作资源。

* **Azure Active Directory Join** (Azure AD Join)：适合访问主要位于云中的资源（如 Office 365）的员工。 Azure AD Join 是 Windows 10 中新增的自助式工作设置体验。
* **域加入**：适合投入本地应用和资源的组织。 在与 Azure AD 相连的情况下，Domain join 将在 Windows 10 中提供得到改善的体验。
* **一个新的简化版 BYOD 体验**：适合想要将工作帐户或学校帐户添加到 Windows，然后轻松地访问个人设备上的资源的用户。

下表向用户和 IT 管理员呈现了各种功能的快照，用于比较企业中使用 Windows 10 可以设置和使用设备的不同方法：

|  | 域加入 | Azure AD Join | 个人设备 |
| --- | --- | --- | --- |
| 适用于工作帐户或学校帐户的 Windows 设备登录。 |是 |是 |否 |
| 用户单一登录 (SSO) 到 Office 365 和 Azure AD 应用。 SSO 使只要登录一次就可以访问组织的资源。 |是 |是 |是 |
| 用户 SSO 到 Kerberos/NTLM 应用。 |是 |受限制 |是，通过 VPN |
| 工作帐户或学校帐户与 Microsoft Passport 和 Windows Hello 结合使用，实现强授权和方便登录。 |是 |是 |是 |
| 使用工作帐户或学校帐户（非 Microsoft 帐户）访问企业版 Windows 应用商店。 |是 |是 |是 |
| 使用工作帐户或学校帐户在设备间符合企业规范地漫游用户设置。 |是 |是 |是 |
| 可以将对组织的应用的访问限于符合组织的设备策略的设备。 |是 |是 |是 |
| 用户自助式预配设备适合“不限地点地投入工作”。 |否 |是 |是 |
| 可以管理设备。 |是，通过 GP/SCCM |是 |是 |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>在 Windows 10 中，将工作专用设备与 Azure AD Join 和域加入结合使用
Windows 10 提供了两种适合工作专用设备访问工作资源的方法：

* Azure AD Join
* 域加入
  
  这两种方法都可以是有效选项，具体取决于组织的需求和要求。 在某些情况下，组织可能会受益于支持这两种方法的部署。

## <a name="when-to-use-azure-active-directory-join"></a>何时使用 Azure Active Directory Join
Azure AD Join 是 Windows 10 中新的自助式工作预配体验。  适合访问主要位于云中的工作资源（如 Office 365）的员工。 它是一种用于为企业配置计算机、平板电脑和手机的简便方法。 通过移动设备管理在各 Windows 平台间使用一致的控制，对设备进行管理。

**出于以下任一原因使用 Azure AD Join**：

* 想要支持路上的员工自助式预配设备。
* 提供给用户诸如平板电脑和手机之类的工作专用移动设备。
* 想要管理 Azure AD（而非 Active Directory）中的一组用户，如临时工、承包商或学生。
* 想要向本地基础结构有限的远程分支机构中的员工提供加入功能。
* 没有本地 Active Directory。

某些组织会将 Azure AD Join 用作部署工作专用设备的主要方法，尤其是当他们将其资源的大部分或全部迁移到云中时。 同时使用 Active Directory 和 Azure AD 的混合型组织还可以选择部署一种方法或另一种，具体取决于用户或部门。

例如，学区和大学可能会管理 Active Directory 中的教工和 Azure AD 中的学生。 一些公司可能想要管理 Azure AD 中的远程办公室或销售部门。 Azure AD Join 和域加入方法在混合型组织内都可以使用。 对于在工作环境中部署设备而言，Azure AD Join 可以是域加入的极好补充。

**如果通常通过云访问企业资源，组织可能会享有额外好处**：

* 可以消除对本地标识基础结构的依赖。
* 可以通过允许自助式配置简化设备部署模型，不必采用映像解决方案。
* 可以使用移动设备管理来管理不同平台间的所有设备。

有关 Azure AD Join 的详细信息，请参阅[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)。

## <a name="when-to-use-domain-join-or-keep-using-it"></a>何时使用域加入（或持续使用它）
过去 15 年，许多组织一直在使用域加入连接工作设备。 它支持用户使用其 Active Directory 工作帐户或学校帐户登录他们的设备。 域加入还允许 IT 管理员集中、全权管理这些设备。 组织通常依靠映像方法分配设备，通常使用 System Center Configuration Manager (SCCM) 或组策略 (GP) 管理这些设备。

**出于以下任一原因，企业应使用域加入（或持续使用它）**：

* 已将 Win32 应用部署到使用 NTLM/Kerberos 的设备。
* 需要使用 GP 或 SCCM/DCM 管理设备。
* 想要继续使用映像解决方案为员工配置设备。

**在与 Azure AD 相连的情况下，Windows 10 中的域加入还带来以下好处**：

* 工作帐户或学校帐户与 Microsoft Passport 和 Windows Hello 结合使用，实现强设备绑定身份验证和方便登录。
* 适用于使用工作帐户或学校帐户（不需要 Microsoft 帐户）的设备访问企业版 Windows 应用商店。
* 在使用工作帐户或学校帐户（不需要 Microsoft 帐户）的设备间符合企业规范地漫游用户设置。
* 可以将对组织的应用的访问限于符合组织的设备策略的设备。

有关 Azure AD Join 的详细信息，请参阅[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)。

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>支持为工作或学校加入个人所有设备
若要在企业中支持 BYOD，Windows 10 使用户可以将工作帐户或学校帐户添加到其计算机、平板电脑或手机。 在用户完成添加工作帐户或学校帐户后，该设备会注册到 Azure AD，并且可选择在组织已完成配置的移动设备管理系统中进行注册。 该目录中会将这些设备显示为“已注册”与“已加入 Azure AD”。 IT 管理员可以基于该信息应用不同的策略，以便根据需要针对个人所有设备执行较宽松的控制（相较于工作专用设备）。

用户可以非常方便地将工作帐户或学校帐户添加到其个人设备。 用户可以在首次访问工作应用程序时执行此操作，也可以通过“设置”菜单手动执行该操作。 该帐户将可对组织的资源执行 SSO。

有关 Azure AD Join 的详细信息，请参阅[体验 Windows 10 时将已加入域的设备连接到 Azure AD](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="enable-domain-join-or-azure-ad-join"></a>启用域加入或 Azure AD Join
前面所述的所有方法（域加入、Azure AD Join 和添加工作帐户或学校帐户）在 Windows 10 用户体验中都有入口点。 不过，在可以进行体验之前，所有方法都需要 IT 管理员启用基础结构中的功能。

## <a name="requirements-for-deploying-azure-ad-join"></a>部署 Azure AD Join 的要求
若要为任一组用户部署 Azure AD Join，需要满足以下条件：

* 一个 Azure AD 订阅。
* 在需要更多功能的情况下，一个 Azure AD Premium 订阅（如移动设备管理自动注册）。
* 移动设备管理 - 例如，一个 Microsoft Intune 订阅、适用于 Office 365 的移动设备管理或与 Azure AD 集成的任一合作伙伴移动设备管理供应商。 （有关详细信息，请参阅本文临近结尾的[常见问题部分](#frequently-asked-questions)）。

如果你的设施为混合型，我们强烈建议你部署 Azure AD Connect 将本地目录扩展到 Azure AD。

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>结合 Azure AD 使用域加入的要求
域加入像往常一样继续工作。 不过，若要通过 Azure AD 中获得好处，需要满足以下条件：

* 一个 Azure AD 订阅。
* 部署 Azure AD Connect 将本地目录扩展到 Azure AD。
* 允许已加入域的设备有条件访问 Azure AD 的策略。
* 当希望能够限制某些设备的访问时，允许访问“已加入域的”设备的策略。
* 启用要求兼容设备的规则的 System Center Configuration Manager 版本 1509 技术预览版。 （请参阅 TechNet 文档和博客文章）。

有关 Windows 10 中的域加入的详细信息，请参阅[体验 Windows 10 时将已加入域的设备连接到 Azure AD](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>使用 BYOD 和“添加工作帐户或学校帐户”的要求
若要启用工作帐户或学校帐户的“自带设备”(BYOD)，需要满足以下条件：

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
不在 Windows 10 中。 在 Windows 8.1 中，已加入域的设备的用户可以将其 Microsoft 帐户（例如，Hotmail、Live、Outlook、Xbox 等）“连接”到其域帐户，启用某些体验（诸如 SSO 到 Live 服务、使用 Windows 应用商店和在设备间漫游用户设置）。 在 Windows 10 中，Microsoft 帐户的“连接”功能已停用。 用户可以将一个或多个 Microsoft 帐户添加为其他帐户，以便支持 SSO 到诸如 Windows 应用商店等使用者服务。 这通过在“设置” > “帐户” > “你的帐户”中实现。

正在从已加入域的 Windows 8.1 设备进行升级和已连接其 Microsoft 帐户的用户会自动将用户已连接的 Microsoft 帐户添加到用户所用的其他帐户列表。

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO5-->


