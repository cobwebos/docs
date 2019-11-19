---
title: 如何在 Azure Active Directory (Azure AD) 中规划混合 Azure Active Directory 加入实现 | Microsoft Docs
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a188349e15c5e4c486d37daed9f8b611a325dcf
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167332"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure Active Directory 联接实现

与用户类似，设备是要保护的，并且随时随时要用来保护资源的另一个核心标识。 若要实现此目的，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件性访问](../active-directory-conditional-access-azure-portal.md)来保护对云和本地资源的访问。

如果你有本地 Active Directory （AD）环境，并且想要将已加入 AD 域的计算机加入到 Azure AD，则可以通过混合 Azure AD 加入来实现此目的。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>先决条件

本文假设你熟悉[Azure Active Directory 中设备标识管理的简介](../device-management-introduction.md)。

> [!NOTE]
> Windows 10 混合 Azure AD 联接所需的最少域控制器版本为 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

|   |   |
| --- | --- |
| ![勾选标记][1] | 查看支持的设备 |
| ![勾选标记][1] | 查看应该知道的事项 |
| ![勾选标记][1] | 查看混合 Azure AD 联接的受控验证 |
| ![勾选标记][1] | 基于标识基础结构选择方案 |
| ![勾选标记][1] | 查看本地 AD UPN 支持混合 Azure AD 联接 |

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
- Windows Server 2019

对于运行 Windows 桌面操作系统的设备，受支持的版本将在[Windows 10 版本信息](https://docs.microsoft.com/windows/release-information/)中列出。 最佳做法是，Microsoft 建议升级到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7。 有关 Windows 7 的支持信息，请参阅对[windows 7 的支持正在结束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2。 有关 Windows Server 2008 和 2008 R2 的支持信息，请参阅[准备 Windows server 2008 支持结束](https://www.microsoft.com/cloud-platform/windows-server-2008)。

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

如果你的环境包含将标识数据同步到多个 Azure AD 租户的单个 AD 林，则当前不支持混合 Azure AD 联接。

如果你的环境使用虚拟桌面基础结构（VDI），请参阅[设备标识和桌面虚拟化](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)。

对于符合 FIPS 的 TPM 2.0，支持混合 Azure AD 联接，不适用于 TPM 1.2。 如果设备具有符合 FIPS 标准的 TPM 1.2，则必须先将其禁用，然后才能继续混合 Azure AD 联接。 Microsoft 不提供任何工具用于为 Tpm 禁用 FIPS 模式，因为它依赖于 TPM 制造商。 请联系你的硬件 OEM 以获得支持。 从 WIndows 10 1903 版本开始，Tpm 1.2 不用于混合 Azure AD 联接，具有这些 Tpm 的设备将视为没有 TPM。

运行域控制器（DC）角色的 Windows Server 不支持混合 Azure AD 联接。

使用凭据漫游或用户配置文件漫游时，Windows 下层设备上不支持混合 Azure AD 联接。

如果你依赖于系统准备工具（Sysprep），并且你使用的是**Windows 之前的 10 1809**映像进行安装，请确保映像不是从已注册到 Azure AD 混合 Azure AD 加入的设备。

如果你依赖于虚拟机（VM）快照来创建其他 Vm，请确保快照不是来自已注册到 Azure AD 的 VM，因为混合 Azure AD 加入。

如果已加入 Windows 10 域的设备[Azure AD 注册](overview.md#getting-devices-in-azure-ad)到你的租户，则可能会导致混合 Azure AD 加入和 Azure AD 注册设备的双重状态。 建议升级到 Windows 10 1803 （应用了 KB4489894）或更高版本来自动处理此方案。 在1803之前的版本中，你将需要手动删除 Azure AD 注册状态，然后才能启用混合 Azure AD join。 在1803及更高版本中，已进行了以下更改，以避免这种双重状态：

- <i>混合 Azure AD 加入设备后</i>，会自动删除任何现有 Azure AD 注册状态。
- 可以通过添加此注册表项-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin "BlockAADWorkplaceJoin" = dword：00000001，阻止已加入域的设备 Azure AD 注册。
- 在 Windows 10 1803 中，如果已配置 Windows Hello 企业版，则用户需要在双重状态清理后重新设置 Windows Hello 企业版。此问题已通过 KB4512509 解决。

> [!NOTE]
> 如果 Azure AD 注册的设备由 Intune 管理，则将不会自动将其删除。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的受控验证

当所有先决条件都准备就绪后，Windows 设备将自动作为 Azure AD 租户中的设备进行注册。 Azure AD 中这些设备标识的状态称为 "混合 Azure AD 联接"。 有关本文中所述概念的详细信息，请参阅[Azure Active Directory 中的设备标识管理简介](overview.md)。

在整个组织中同时启用混合 Azure AD 联接之前，组织可能需要对其进行控制验证。 查看对[混合 Azure AD 联接的受控验证一](hybrid-azuread-join-control.md)文，了解如何实现它。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>基于标识基础结构选择方案

混合 Azure AD 联接适用于、托管和联合环境，具体取决于 UPN 是可路由还是不可路由。 有关支持的方案，请参阅表的底部。  

### <a name="managed-environment"></a>托管环境

可使用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)通过[密码哈希同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 或[直通身份验证 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

### <a name="federated-environment"></a>联合环境

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **WIAORMULTIAUTHN 声明：** 为 Windows 下层设备执行混合 Azure AD 联接需要此声明。
- **WS 信任协议：** 需要此协议来对 Windows 当前混合 Azure AD 与 Azure AD 进行联接的设备进行身份验证。 使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 只能作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 可以通过 AD FS 管理控制台中的“服务” **“终结点”查看已启用哪些终结点。**  > 

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 该向导可让你显著简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](hybrid-azuread-join-manual.md)。 

根据与标识基础结构匹配的方案，请参阅：

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的本地 AD UPN 支持

有时，本地 AD UPN 可能不同于 Azure AD UPN。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- 可路由 UPN：可路由的 UPN 具有有效的验证域，该域已注册到域注册机构。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的本地 AD 中的主域
- 不可路由的 UPN：不可路由的 UPN 没有经过验证的域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 说明 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | 托管 | 从 1803 版本开始 | Azure AD 不支持 Windows 锁屏上的 SSPR |
| 非可路由的 | 托管 | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
> 为[托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
