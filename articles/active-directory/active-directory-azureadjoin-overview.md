---
title: "通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备 | Microsoft 文档"
description: "详细阐述 Windows 10 设备如何利用 Azure AD Join 在 Azure Active Directory 上注册。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 0cd4942f-7d54-474e-bd12-8e6764b0d42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: d3a3d3efe1c43caff3b8d2956c14e8c90d05d22b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/06/2017


---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备
## <a name="what-is-azure-active-directory-join"></a>什么是 Azure Active Directory Join
Azure Active Directory Join (Azure AD Join) 是在 Azure Active Directory 中注册公司拥有的设备，以实现集中式设备管理的功能。 它使用户（例如员工和学生）可以通过 Azure Active Directory 连接到企业云。 这样就简化了 Windows 部署，可从任何 Windows 设备（公司拥有的和个人拥有的设备 (BYOD)）访问组织应用与资源。

Azure AD Join 面向云优先/仅限云的企业 -- 通常是不具备本地 Windows Server Active Directory 基础结构的中小型企业。 也就是说，Azure AD Join 也能够并将供大型组织在无法加入传统域的设备上（例如移动设备）使用，或由主要需要访问 Office 365 或其他与 Azure AD 相集成的 SaaS 应用的用户使用。

尽管传统域在能够加入域的设备上仍能提供最佳的本地体验，但 Azure AD Join 适用于无法加入域的设备。 Azure AD Join 也适用于在云中管理用户。 它使用移动设备管理功能而不是传统域管理工具（如组策略和 System Center Configuration Manager (SCCM)）进行这种管理。

![概述使用本地 Active Directory 和 Azure AD 的企业设备和个人设备](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join"></a>企业为何应该采用 Azure AD Join？
* **主要在云中展开的业务**：如果已经或即将迁移到可减少本地占用空间且想要在云中进行更多操作的模型，则 Azure AD Join 将很有帮助。 或许你已经手动或通过同步本地 Active Directory 的方式创建了 Azure AD 帐户。 无论如何，你在 Azure AD 中已有一个可用于登录 Windows 10 的帐户。 用户可以通过现成体验 (OOBE) 或“设置”菜单，将其计算机加入 Azure AD。 加入后，用户可以在浏览器或 Office 应用程序中通过单一登录 (SSO) 访问云资源（例如 Office 365）。
* **教育机构**：我们经常听到的一种方案是教育机构采用两种用户类型：教职员和学生。 教职员被视为组织中较长期的成员，因此需要为他们创建本地帐户。 但学生是组织中期限较短的成员，因此可在 Azure AD 中管理这些用户。 这意味着，可以将目录范围推送到云而不是存储在本地。 此外，这还意味着学生可以使用其 Azure AD 帐户登录 Windows，并在浏览器或 Office 应用程序中获取 Office 365 资源的访问权限。
* **零售业**：客户提到的另一种方案是他们想要更轻松地管理季节性工作者。  再次提醒，可以将较长期的全职员工的帐户创建为已加入域的计算机上的本地帐户。 但季节性工作者是组织中期限较短的成员，因而需要在可更轻松转移用户许可证的位置管理这些用户。 在具有 Office 365 许可证的云中创建这些用户帐户，让这些用户能够由于使用 Azure AD 帐户登录 Windows 和 Office 应用程序而获益。 同时，在他们离开公司之后对他们的许可证保持更大的灵活性。
* **其他业务**：即使在本地 Active Directory 中维护用户，也仍可以享受到将用户加入 Azure AD 的好处。 这是因为，Azure AD 为 Azure AD 和本地资源提供简化的加入体验、高效的设备管理、自动的移动设备管理注册和单一登录功能。  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Azure AD Join 提供哪些功能？
使用 Azure AD Join 可以获得以下优势：

* **自我预配企业拥有的设备**：使用 Windows 10，用户无需 IT 干预就可以在现成的体验中配置全新的拆封授权设备。
* **支持现代外形规格**：Azure AD Join 将在未具备传统域加入功能的设备上运行。  
* **支持现有组织帐户**：用户不再需要创建和维护个人 Microsoft 帐户，即可在公司发放的设备上获得最佳体验，就像在 Windows 8 上工作一样。 他们可以改为在 Azure AD 中使用现有的工作帐户。 对许多组织而言，这基本上意味着用户可以使用他们用来访问 Office 365 的相同凭据来设置和登录 Windows。
* **自动的移动设备管理注册**：设备可以在连接到 Azure AD 时自动注册到移动设备管理中。 此过程适用于 Microsoft Intune 与合作伙伴移动设备管理解决方案。 使用 Intune 执行设备管理时，IT 管理员可在 SCCM 管理控制台中监视/管理已加入 Azure AD 的设备以及已加入域的设备。
* **单一登录公司资源**：用户享有从 Windows 桌面到云中应用和资源的单一登录（例如 Office 365），以及数千个通过 [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md) 依赖 Azure AD 进行身份验证的企业应用程序。 当已加入 Azure AD 的企业拥有设备位于企业网络时，这类设备也享有本地资源的 SSO，并且在通过 [Azure AD 应用程序代理](https://msdn.microsoft.com/library/azure/Dn768219.aspx)公开这些资源时，也可从任何地方享有 SSO。
* **OS 状态漫游**：辅助功能设置、网站和 Wi-Fi 密码等设置都会在企业拥有的设备上同步，且无需使用个人 Microsoft 帐户。
* **符合企业要求的 Windows 应用商店**：Windows 应用商店支持通过 Azure AD 帐户获取和许可应用。 组织可以获取批量许可应用，并提供给组织中的用户使用。

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>不同的设备如何与 Azure AD Join 配合工作？
| 企业设备（已加入本地域） | 企业设备（已加入云） | 个人设备 |
| --- | --- | --- |
| 用户使用工作凭据登录 Windows（就像平时一样）。 |用户可以使用 Azure AD 中管理的工作凭据登录 Windows。 这与以下三种情况中的企业设备相关： <ol><li>组织没有本地 Active Directory（例如，小型企业）。</li><li>组织不是在 Active Directory 中创建所有用户帐户（例如，学生、顾问、季节工的帐户）。</li><li>组织中有无法加入（本地）域的企业设备，例如，运行移动 SKU 的手机或平板电脑（例如，带到工厂/零售现场的辅助设备）。</li></ol> Azure AD Join 在托管和联合组织中支持加入企业设备。 |用户使用其个人 Microsoft 帐户凭据登录 Windows（无变化）。 |
| 用户可以访问设置漫游和 Windows 应用商店。 这些服务可以使用工作帐户来运行，而无需个人 Microsoft 帐户。 这要求组织将其本地 Active Directory 连接到 Azure AD。 |用户可以执行自助设置。 他们可以通过其工作帐户完整获得初次运行体验 (FRX)，这可为预配设备的 IT 提供另一种方法，不过，这两种方法均受支持。 |用户可轻松添加要在 Active Directory 或 Azure AD 中管理的工作帐户。 |
| 用户可以从桌面通过 SSO 访问工作应用、网站和资源 -- 包括使用 Azure AD 进行身份验证的本地资源和云应用。 |设备自动在企业目录 (Azure AD) 中注册，并自动在移动设备管理中注册。 （Azure AD 高级版功能。） |用户可以使用此工作帐户通过 SSO 访问各种应用和网站/资源。 |
| 用户可以添加个人 Microsoft 帐户来访问个人图片和文件，而不影响企业数据。 （漫游设置继续与其工作帐户配合工作。）Microsoft 帐户可启用 SSO，不再驱动设置漫游。 |用户可以在 winlogon 上执行自助密码重置 (SSPR)，意味着他们可以重置忘记的密码。 （Azure AD 高级版功能。） |用户有权访问企业 Windows 应用商店，因此可在他们的个人设备上获取和使用业务线应用。 |

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [通过 Microsoft Passport 在不使用密码的情况下进行身份验证](active-directory-azureadjoin-passport.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)


