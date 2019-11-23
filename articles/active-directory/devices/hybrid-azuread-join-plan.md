---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
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
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379320"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

与用户类似，设备是要保护的，并且随时随时要用来保护资源的另一个核心标识。 若要实现此目的，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>必备组件

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>计划实施

若要规划混合 Azure AD 实现，应做好以下准备：

|   |   |
| --- | --- |
| ![勾选标记][1] | 查看支持的设备 |
| ![勾选标记][1] | 查看应该知道的事项 |
| ![勾选标记][1] | Review controlled validation of hybrid Azure AD join |
| ![勾选标记][1] | Select your scenario based on your identity infrastructure |
| ![勾选标记][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>托管环境

可使用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)通过[密码哈希同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 或[直通身份验证 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

### <a name="federated-environment"></a>联合环境

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. 使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 只能作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 可以通过 AD FS 管理控制台中的“服务” > “终结点”查看已启用哪些终结点。

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 该向导可让你显著简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](hybrid-azuread-join-manual.md)。 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

有时，本地 AD UPN 可能不同于 Azure AD UPN。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的本地 AD 中的主域
- Non-routable UPN: A non-routable UPN does not have a verified domain. 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | 托管 | 从 1803 版本开始 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| 非可路由的 | 托管 | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
