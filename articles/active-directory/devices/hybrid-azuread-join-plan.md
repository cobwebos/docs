---
title: 如何在 Azure Active Directory (Azure AD) 中规划混合 Azure Active Directory 加入实现 | Microsoft Docs
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64dd8067654246f7c9a077d027c068df820f439d
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688696"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure Active Directory 加入实现

用户类似，设备是你想要保护和使用它来保护你的资源，在任何时间并从任何位置的另一个核心标识。 可以通过自带并使用以下方法之一在 Azure AD 中管理设备标识来实现此目标：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)保护对云和本地资源的访问。

如果必须在本地 Active Directory (AD) 环境，并且你想要将你的 AD 已加入域的计算机加入到 Azure AD，可以通过执行混合 Azure AD 联接来实现此目的。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>必备组件

本文假定你熟悉[Azure Active Directory 中的设备标识管理简介](../device-management-introduction.md)。

> [!NOTE]
> 所需的最低所需 Windows 10 混合 Azure AD join 是 Windows Server 2008 R2 域功能和林功能级别。

## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

|   |   |
| --- | --- |
| ![勾选标记][1] | 查看支持的设备 |
| ![勾选标记][1] | 查看应该知道的事项 |
| ![勾选标记][1] | 查看混合 Azure AD 联接的受控的验证 |
| ![勾选标记][1] | 选择你的方案基于标识基础结构 |
| ![勾选标记][1] | 查看在本地 AD UPN 支持混合的 Azure AD 联接 |

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
- Windows Server 2019

对于运行 Windows 桌面操作系统的设备，这篇文章中列出受支持的版本[Windows 10 发布信息](https://docs.microsoft.com/windows/release-information/)。 最佳做法是，Microsoft 建议您升级到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7. 有关在 Windows 7 上支持信息，请参阅此文章[结束支持适用于 Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

如果你的环境中包含的单个 AD 林同步到多个 Azure AD 租户的标识数据，是目前不支持混合 Azure AD 联接。

混合 Azure AD 联接当前不支持使用虚拟桌面基础结构 (VDI) 时。

与 FIPS 兼容的 Tpm 不支持混合 Azure AD。 如果你的设备具有与 FIPS 兼容的 Tpm，则必须在继续进行混合 Azure AD 加入之前禁用它们。 禁用 FIPS 模式下的 Tpm，因为它是依赖于 TPM 制造商，Microsoft 不提供任何工具。 请联系您的硬件 OEM 获取支持。

运行域控制器 (DC) 角色的 Windows Server 不支持混合 Azure AD 联接。

使用凭据漫游用户配置文件时在 Windows 下层设备上不支持混合 Azure AD 联接。

如果将依赖于系统准备工具 (Sysprep) 并使用**pre-Windows 10 1809年**安装映像，请确保该映像不是来自已注册到为混合 Azure AD 联接 Azure AD 的设备。

如果您依赖于要创建更多虚拟机的虚拟机 (VM) 快照，请确保该快照不是来自已注册到为混合 Azure AD 联接 Azure AD 的 VM。

如果加入 Windows 10 域的设备在你的租户中[已注册 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices)，强烈建议在启用混合 Azure AD 加入之前删除该状态。 从 Windows 10 1809 版本开始，进行了以下更改来避免此双重状态：

- 在设备加入混合 Azure AD 后，会自动删除任何现有的已注册 Azure AD 状态。
- 可阻止您加入的域的设备成为 Azure AD 注册通过添加此注册表项-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin，"BlockAADWorkplaceJoin"= dword: 00000001。
- 此更改现已可供 Windows 10 1803年发布与 KB4489894 应用。 但是，如果您有 Windows hello 企业版配置，用户则需要以重新设置 Windows hello 企业版后双状态清理。


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的受控的验证

当所有系统必备组件到位，将自动为你的 Azure AD 租户中的设备注册 Windows 设备。 Azure AD 中的这些设备标识状态称为混合 Azure AD 联接。 可以在文章中找到有关本文中所述的概念的详细信息[Azure Active Directory 中的设备标识管理简介](overview.md)和[计划你的混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)。

组织可能想要执行混合 Azure AD 联接的一次性其整个组织启用之前受控的验证。 查看文章[控制的混合 Azure AD 联接验证](hybrid-azuread-join-control.md)若要了解如何完成该操作。


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>选择你的方案基于标识基础结构

混合 Azure AD 联接适用于，托管和联合的环境。  

### <a name="managed-environment"></a>托管环境

可以是托管的环境部署通过[密码哈希同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)或[传递通过身份验证 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta)与[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)。

这些方案不需要配置联合服务器进行身份验证。

### <a name="federated-environment"></a>联合环境

联合的环境中应具有的标识提供程序支持以下要求：

- **WS 信任协议：** 此协议是需要对 Windows 进行身份验证当前的混合 Azure AD 加入与 Azure AD 的设备。
- **WIAORMULTIAUTHN 声明：** 需要执行的 Windows 下层设备的混合 Azure AD 联接此声明。

如果必须使用 Active Directory 联合身份验证服务 (AD FS) 联合的环境，则已支持上述要求。

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 使用该向导能够大幅简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual)。 

基于匹配标识基础结构的方案，请参阅：

- [配置混合 Azure Active Directory 的联合环境中的联接](hybrid-azuread-join-federated-domains.md)
- [配置托管环境的混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>查看本地 AD UPN 支持用于混合 Azure AD 联接

有时，本地 AD UPN 可能不同于 Azure AD UPN。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- 可路由的 UPN：可路由的 UPN 具有已向域注册机构注册的有效的已验证域。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的本地 AD 中的主域
- 非可路由的 UPN：非可路由的 UPN 没有已验证域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | 托管 | 不支持 | |
| 非可路由的 | 托管 | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关联合的环境配置混合 Azure Active Directory 加入](hybrid-azuread-join-federated-domains.md)
> [托管环境的配置混合 Azure Active Directory join](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
