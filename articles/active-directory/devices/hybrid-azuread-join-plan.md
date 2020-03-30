---
title: 计划混合 Azure 活动目录联接 - Azure 活动目录
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
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129254"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure 活动目录联接实现

与用户类似，设备是要保护的，并且随时随时要用来保护资源的另一个核心标识。 若要实现此目的，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，您可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)来保护对云和本地资源的访问。

如果您有本地活动目录 （AD） 环境，并且希望将 AD 域联接的计算机加入到 Azure AD，则可以通过执行混合 Azure AD 联接来实现此目的。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>先决条件

本文假定您熟悉[Azure 活动目录中的设备标识管理简介](../device-management-introduction.md)。

> [!NOTE]
> Windows 10 混合 Azure AD 联接的最低需要域控制器版本是 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

|   |   |
| --- | --- |
| ![勾选标记][1] | 查看支持的设备 |
| ![勾选标记][1] | 查看应该知道的事项 |
| ![勾选标记][1] | 查看混合 Azure AD 联接的控制验证 |
| ![勾选标记][1] | 根据您的身份基础结构选择方案 |
| ![勾选标记][1] | 查看本地 AD UPN 对混合 Azure AD 联接的支持 |

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
  - **注意**： Azure 国家云客户需要版本 1809
- Windows Server 2019

对于运行 Windows 桌面操作系统的设备，本文中列出了受支持的版本[Windows 10 发布信息](/windows/release-information/)。 作为最佳实践，Microsoft 建议您升级到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，请参阅对[Windows 7 的支持已结束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows 服务器 2008 R2。 有关 Windows 服务器 2008 和 2008 R2 的支持信息，请参阅[为 Windows 服务器 2008 结束支持做好准备](https://www.microsoft.com/cloud-platform/windows-server-2008)。

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

### <a name="unsupported-scenarios"></a>不支持的方案
- 如果环境由单个 AD 林组成，将标识数据同步到多个 Azure AD 租户，则当前不支持混合 Azure AD 联接。

- 运行域控制器 （DC） 角色的 Windows 服务器不支持混合 Azure AD 联接。

- 使用凭据漫游或用户配置文件漫游或强制配置文件时，Windows 低级设备不支持混合 Azure AD 联接。

- 服务器核心操作系统不支持任何类型的设备注册。

### <a name="os-imaging-considerations"></a>操作系统映像注意事项
- 如果依赖于系统准备工具 （Sysprep），并且使用**Windows 10 1809 前**映像进行安装，请确保映像不是来自已在 Azure AD 注册为混合 Azure AD 联接的设备。

- 如果依赖于虚拟机 （VM） 快照来创建其他 VM，请确保快照不是来自已在 Azure AD 注册为混合 Azure AD 联接的 VM。

- 如果使用[统一写入筛选器](/windows-hardware/customize/enterprise/unified-write-filter)和在重新启动时清除磁盘更改的类似技术，则必须在设备加入混合 Azure AD 后应用这些技术。 在混合 Azure AD 联接完成之前启用此类技术将导致设备在每次重新启动时未连接

### <a name="handling-devices-with-azure-ad-registered-state"></a>处理具有 Azure AD 注册状态的设备
如果 Windows 10 域加入的设备是[Azure AD 注册](overview.md#getting-devices-in-azure-ad)给租户，则可能导致混合 Azure AD 加入和 Azure AD 注册设备的双重状态。 我们建议升级到 Windows 10 1803（应用 KB4489894）或以上，以自动解决此方案。 在 1803 以前的版本中，在启用混合 Azure AD 联接之前，需要手动删除 Azure AD 注册状态。 在 1803 及以上版本中，已进行了以下更改，以避免这种双重状态：

- <i>在设备加入混合 Azure AD 并且同一用户登录 后，</i>将自动删除用户的任何现有 Azure AD 注册状态。 例如，如果用户 A 在设备上具有 Azure AD 注册状态，则仅当用户 A 登录到设备时，才会清除用户 A 的双状态。 如果同一设备上有多个用户，则当这些用户登录时，将单独清理双状态。
- 您可以通过添加此注册表项 - HKLM_SOFTWARE_政策\微软_Windows_工作区联接，"BlockAAD WorkplaceJoin"\dword：00000001 来防止域加入的设备被 Azure AD 注册。
- 在 Windows 10 1803 中，如果配置了适用于企业的 Windows Hello，则用户需要在双状态清理后重新设置适用于企业的 Windows Hello。此问题已通过 KB4512509 得到解决

> [!NOTE]
> 如果 Azure AD 注册设备由 Intune 管理，则不会自动删除该设备。

### <a name="additional-considerations"></a>其他注意事项
- 如果您的环境使用虚拟桌面基础结构 （VDI），请参阅[设备标识和桌面虚拟化](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)。

- 符合 FIPS 的 TPM 2.0 支持混合 Azure AD 联接，TPM 1.2 不支持混合 Azure AD 联接。 如果设备具有符合 FIPS 的 TPM 1.2，则必须在继续混合 Azure AD 联接之前禁用它们。 Microsoft 不提供任何用于禁用 TPM FIPS 模式的工具，因为它依赖于 TPM 制造商。 请联系您的硬件 OEM 以获取支持。 

- 从 Windows 10 1903 版本开始，TPM 1.2 不与混合 Azure AD 联接一起使用，并且具有这些 TPM 的设备将被视为没有 TPM。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的控制验证

当所有先决条件都到位时，Windows 设备将自动注册为 Azure AD 租户中的设备。 Azure AD 中的这些设备标识的状态称为混合 Azure AD 联接。 有关本文中介绍的概念的详细信息，请参阅[Azure 活动目录中的设备标识管理简介](overview.md)。

组织可能需要对混合 Azure AD 联接进行受控验证，然后再在整个组织中同时启用它。 查看混合[Azure AD 联接的受控验证](hybrid-azuread-join-control.md)文章，了解如何完成它。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>根据您的身份基础结构选择方案

混合 Azure AD 联接适用于两者、托管和联合环境，具体取决于 UPN 是可路由还是不可路由。 有关支持的方案，请参阅页面底部。  

### <a name="managed-environment"></a>托管环境

可使用[无缝单一登录](/azure/active-directory/hybrid/how-to-connect-sso)通过[密码哈希同步 (PHS)](/azure/active-directory/hybrid/whatis-phs) 或[直通身份验证 (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) 来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

### <a name="federated-environment"></a>联合环境

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **维奥多奥奥奥森索赔：** 此声明需要为 Windows 低级设备执行混合 Azure AD 联接。
- **WS-信任协议：** 此协议需要通过 Azure AD 对 Windows 当前混合 Azure AD 联接的设备进行身份验证。 使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 只能作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 您可以在**服务** > **终结点**下通过 AD FS 管理控制台查看启用哪些终结点。

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 该向导可让你显著简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](hybrid-azuread-join-manual.md)。 

根据与您的标识基础结构匹配的方案，请参阅：

- [为联合环境配置混合 Azure 活动目录联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure 活动目录联接](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>查看本地 AD 用户 UPN 对混合 Azure AD 联接的支持

有时，本地 AD 用户 UPN 可能不同于 Azure AD UPN。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](/azure/security/fundamentals/choose-ad-authn)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- 可路由用户 UPN：可路由的 UPN 具有有效的已验证域，该域注册商注册。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](/azure/active-directory/fundamentals/add-custom-domain)的本地 AD 中的主域
- 不可路由用户 UPN：不可路由的 UPN 没有经过验证的域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

> [!NOTE]
> 本节中的信息仅适用于本地用户 UPN。 它不适用于本地计算机域后缀（例如：计算机1.contoso.local）。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | 托管 | 从 1803 版本开始 | 通常可用，不支持 Windows 锁屏上的 Azure AD SSPR |
| 非可路由的 | 托管 | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为联合环境](hybrid-azuread-join-federated-domains.md)
> 配置混合 Azure 活动目录联接[为托管环境配置混合 Azure 活动目录联接](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
