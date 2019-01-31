---
title: 如何在 Azure Active Directory (Azure AD) 中规划混合 Azure Active Directory 加入实现 | Microsoft Docs
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 085f95e1df67a12afac5c327b4368efd275600b3
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100166"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure Active Directory 加入实现

与用户类似，设备将成为要保护的另一标识，并且也用于随时保护资源和位置。 为此，可通过下述某一方法将设备的标识引入 Azure AD：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)保护对云和本地资源的访问。

如果你有本地 Active Directory 环境，并且想要将已加入域的设备联接到 Azure AD，则可以通过配置联接到混合 Azure AD 的设备来实现。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 


## <a name="prerequisites"></a>先决条件

本文假设读者已阅读 [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)。


## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

|   |   |
|---|---|
|![勾选标记][1]|查看支持的设备|
|![勾选标记][1]|查看应该知道的事项|
|![勾选标记][1]|查看如何控制设备的混合 Azure AD 加入|
|![勾选标记][1]|选择方案|


 

## <a name="review-supported-devices"></a>查看支持的设备 

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

**Windows 新式设备**

- Windows 10
    
- Windows Server 2016


对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。



 **Windows 下层设备**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。



## <a name="review-things-you-should-know"></a>查看应该知道的事项

如果环境中包含已将标识数据同步到多个 Azure AD 租户的单个林，则你无法使用混合 Azure AD 加入。

如果依赖系统准备工具 (Sysprep)，请确保从尚未针对混合 Azure AD 加入进行配置的 Windows 安装创建映像。

如果依赖于使用虚拟机 (VM) 快照来创建更多的 VM，请确保使用尚未针对混合 Azure AD 加入进行配置的 VM 快照。

Windows 下层设备的混合 Azure AD 加入：

- 在非联合环境中，通过 [Azure Active Directory 无缝单一登录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)支持。 

- 使用 Azure AD 直通身份验证但不使用无缝单一登录时，不支持。

- 使用凭据漫游或用户配置文件漫游或使用虚拟桌面基础结构 (VDI) 时，不支持。


不支持注册运行域控制器 (DC) 角色的 Windows Server。

如果组织需要通过经身份验证的出站代理访问 Internet，则必须确保 Windows 10 计算机能够成功对出站代理进行身份验证。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此，必须使用计算机上下文配置出站代理身份验证。


混合 Azure AD 加入是自动将已加入域的本地设备注册到 Azure AD 的过程。 在某些情况下，你并不希望自动注册所有设备。 如果遇到这种情况，请参阅[如何控制设备的混合 Azure AD 加入](hybrid-azuread-join-control.md)。

如果加入 Windows 10 域的设备在你的租户中[已注册 Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/devices/overview#azure-ad-registered-devices)，则在启用混合 Azure AD 加入之前应当考虑删除该状态。 不支持设备具有双重状态，即同时具有混合 Azure AD 加入与已注册 Azure AD 状态。 从 Windows 10 1809 版本开始，进行了以下更改来避免此双重状态： 
 - 在设备加入混合 Azure AD 后，会自动删除任何现有的已注册 Azure AD 状态。 
 - 可以通过添加以下注册表项阻止将已加入域的设备注册到 Azure AD - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>查看如何控制设备的混合 Azure AD 加入

混合 Azure AD 联接是自动将已加入域的本地设备注册到 Azure AD 的过程。 在某些情况下，你并不希望自动注册所有设备。 例如，在初始部署期间验证一切是否都按预期运行时，这一点就非常必要。

有关详细信息，请参阅[如何控制设备的混合 Azure AD 加入](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>选择方案

可为以下方案配置混合 Azure AD 加入：

- 托管域
- 联合域  



如果环境包含托管域，则混合 Azure AD 加入支持：

- 直通身份验证 (PTA)

- 密码哈希同步 (PHS)

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 该向导可让你显著简化配置过程。 有关详细信息，请参阅：

- [为联盟域配置混合 Azure Active Directory 加入](hybrid-azuread-join-federated-domains.md)


- [为托管域配置混合 Azure Active Directory 加入](hybrid-azuread-join-managed-domains.md)


 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](../device-management-hybrid-azuread-joined-devices-setup.md)。 


## <a name="alternate-login-id-support-in-hybrid-azure-ad-join"></a>混合 Azure AD 加入中的备用登录 ID 支持

Windows 10 混合 Azure AD 加入根据备用登录 ID 的类型、[身份验证方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、域类型和 Windows 10 版本对[备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 提供有限支持。 你的环境中可以存在两种类型的备用登录 ID：

 - 可路由的备用登录 ID：可路由的备用登录 ID 具有向域注册机构注册的有效已验证域。 例如，如果 contoso.com 是主域，则 contoso.org 和 contoso.co.uk 是 Contoso拥有的且[已在 Azure AD 中验证的](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)有效域。
 
 - 非可路由的备用登录 ID：非可路由的备用登录 ID 没有已验证域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是主域，则 contoso.local 在 Internet 中不是可验证的域，但可在 Contoso 的网络内使用。
 
下表提供了 Windows 10 混合 Azure AD 加入中对这两种备用登录 ID 的支持的详细信息

|备用登录 ID 的类型|域类型|Windows 10 版本|说明|
|-----|-----|-----|-----|
|可路由的|联合 |从 1703 版本开始|正式发布|
|可路由的|托管|从 1709 版本开始|当前为个人预览版。 不支持 Azure AD SSPR |
|非可路由的|联合|从 1803 版本开始|正式发布|
|非可路由的|托管|不支持||



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为联合域配置混合 Azure Active Directory 加入](hybrid-azuread-join-federated-domains.md)
> [为托管域配置混合 Azure Active Directory 加入](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
