---
title: 为联盟域配置混合 Azure Active Directory 加入 | Microsoft Docs
description: 了解如何为联盟域配置混合 Azure Active Directory 加入。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204692"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>教程：为联盟域配置混合 Azure Active Directory 加入

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 要实现此目的，可以使用下述某种方法将设备标识引入 Azure Active Directory (Azure AD) 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

将设备引入 Azure AD 可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)保护对云和本地资源的访问。

本教程介绍如何使用 Active Directory 联合身份验证服务 (AD FS) 在联合环境中为已加入 AD 域的计算机设备配置混合 Azure AD 加入。

> [!NOTE]
> 如果联合环境使用的标识提供者不是 AD FS，则需要确保该标识提供者支持 WS-Trust 协议。 需要在 Azure AD 中使用 WS-Trust 对当前已加入混合 Azure AD 的 Windows 设备进行身份验证。 如果需要将 Windows 下层设备加入混合 Azure AD，则标识提供者需要支持 WIAORMULTIAUTHN 声明。 

学习如何：

> [!div class="checklist"]
> * 配置混合 Azure AD 联接
> * 启用 Windows 下层设备
> * 验证注册
> * 故障排除

## <a name="prerequisites"></a>先决条件

本教程假定你熟悉以下文章：

- [什么是设备标识？](overview.md)
- [如何规划混合 Azure AD 加入实现](hybrid-azuread-join-plan.md)
- [如何以受控方式验证混合 Azure AD 加入](hybrid-azuread-join-control.md)

要配置本教程中的方案，需具备以下项：

- 带 AD FS 的 Windows Server 2012 R2
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 或更高版本

自 1.1.819.0 版起，Azure AD Connect 包含用于配置混合 Azure AD 加入的向导。 该向导显著简化了配置过程。 相关向导：

- 配置设备注册的服务连接点 (SCP)
- 备份现有的 Azure AD 信赖方信任
- 更新 Azure AD 信任中的声明规则

本文中的配置步骤需要使用 Azure AD Connect 向导。 如果安装有较旧版本的 Azure AD Connect，需要将其升级到 1.1.819 或更高版本才能使用该向导。 如果无法安装最新版本的 Azure AD Connect，请参阅[如何手动配置混合 Azure AD 加入](hybrid-azuread-join-manual.md)。

混合 Azure AD 加入要求设备能够从组织的网络中访问以下 Microsoft 资源：  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- 组织的安全令牌服务 (STS)（联合域）
- `https://autologon.microsoftazuread-sso.com`（如果使用或计划使用无缝 SSO）

从 Windows 10 1803 开始，如果无法在使用 AD FS 的联合环境中实现即时混合 Azure AD 加入，我们将依赖 Azure AD Connect 同步 Azure AD 中的计算机对象，该计算机对象随后用于完成混合 Azure AD 加入的设备注册。 验证 Azure AD Connect 是否已将要加入混合 Azure AD 的设备的计算机对象同步到 Azure AD。 如果这些计算机对象属于特定组织单位 (OU)，则还需要在 Azure AD Connect 中配置这些 OU 以进行同步。 要详细了解如何使用 Azure AD Connect 同步计算机对象，请参阅[使用 Azure AD Connect 配置筛选](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

如果你的组织需要通过出站代理访问 Internet，Microsoft 建议[实施 Web 代理自动发现 (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))，以使 Windows 10 计算机在 Azure AD 进行设备注册。 如果在配置和管理 WPAD 时遇到问题，请参阅[自动检测故障排除](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))。 

如果不使用 WPAD 并希望在计算机上配置代理设置，则可以从 Windows 10 1709 开始。 有关详细信息，请参阅[使用组策略对象 (GPO) 配置 WinHTTP 设置](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)。

> [!NOTE]
> 如果使用 WinHTTP 设置在计算机上配置代理设置，则无法连接到所配置的代理的任何计算机将无法连接到 Internet。

如果组织需要通过经身份验证的出站代理访问 Internet，则必须确保 Windows 10 计算机能够成功验证出站代理的身份。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此必须使用计算机上下文配置出站代理身份验证。 根据配置要求使用相应的出站代理提供程序。

## <a name="configure-hybrid-azure-ad-join"></a>配置混合 Azure AD 联接

要使用 Azure AD Connect 配置混合 Azure AD 加入，需具备以下各项：

- Azure AD 租户的全局管理员凭据  
- 每个林的企业管理员凭据
- AD FS 管理员凭据

**使用 Azure AD Connect 配置混合 Azure AD 加入**：

1. 启动 Azure AD Connect，然后选择“配置”  。

   ![欢迎使用](./media/hybrid-azuread-join-federated-domains/11.png)

1. 在“其他任务”页面上，选择“配置设备选项”，然后单击“下一步”    。

   ![其他任务](./media/hybrid-azuread-join-federated-domains/12.png)

1. 在“概述”页面上，选择“下一步”   。

   ![概述](./media/hybrid-azuread-join-federated-domains/13.png)

1. 上“连接到 Azure AD”页面上，输入 Azure AD 租户的全局管理员凭据，然后选择“下一步”   。

   ![连接到 Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. 在“设备选项”页面上，选择“配置混合 Azure AD 加入”，然后选择“下一步”    。

   ![设备选项](./media/hybrid-azuread-join-federated-domains/15.png)

1. 在“SCP”页面上，完成以下步骤，然后选择“下一步”   ：

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. 选择林。
   1. 选择身份验证服务。 除非你的组织仅有 Windows 10 客户端且你已配置计算机/设备同步或者你的组织使用无缝 SSO，否则必须选择“AD FS 服务器”  。
   1. 单击“添加”，输入企业管理员凭据  。

1. 在“设备操作系统”页面上，选择 Active Directory 环境中设备使用的操作系统，然后选择“下一步”   。

   ![设备操作系统](./media/hybrid-azuread-join-federated-domains/17.png)

1. 在“联合身份验证配置”页面上，输入 AD FS 管理员凭据，然后选择“下一步”   。

   ![联合身份验证配置](./media/hybrid-azuread-join-federated-domains/18.png)

1. 在“准备好配置”页面上，选择“配置”   。

   ![已准备好配置](./media/hybrid-azuread-join-federated-domains/19.png)

1. 在“配置完成”页面上，选择“退出”   。

   ![配置完成](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则需要：

- 配置设备注册的本地 Intranet 设置
- 安装适用于 Windows 下层计算机的 Microsoft Workplace Join

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>配置设备注册的本地 Intranet 设置

要成功完成 Windows 下层设备的混合 Azure AD 加入，同时避免在设备向 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，从而在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

- `https://device.login.microsoftonline.com`
- 组织的 STS（联合域）
- `https://autologon.microsoftazuread-sso.com`（适用于无缝 SSO）

此外，还需要在用户的本地 Intranet 区域中启用“允许通过脚本更新状态栏”  。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>安装适用于 Windows 下层计算机的 Microsoft Workplace Join

要注册 Windows 下层设备，组织必须安装[适用于 Windows 10 计算机的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 适用于 Windows 10 计算机的 Microsoft Workplace Join 在 Microsoft 下载中心提供。

可以使用  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 `quiet` 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 在 Azure AD 中进行身份验证后，此任务便会使用用户凭据将设备静默加入 Azure AD。

## <a name="verify-the-registration"></a>验证注册

要验证 Azure 租户中的设备注册状态，可使用 [Azure Active Directory PowerShell 模块](/powershell/azure/install-msonlinev1?view=azureadps-2.0)中的 [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) cmdlet  。

使用 Get-MSolDevice cmdlet 检查服务详细信息时  ：

- 必须存在其**设备 ID** 与 Windows 客户端上的 ID 相匹配的对象。
- DeviceTrustType 的值必须是“已加入域”   。 此设置相当于 Azure AD 门户中“设备”页面下的“已加入混合 Azure AD”状态   。
- 对于采用了条件访问的设备，“已启用”值必须为 True，“DeviceTrustLevel”值必须为“Managed”     。

**检查服务详细信息**：

1. 以管理员身份打开 Windows PowerShell。
1. 输入 `Connect-MsolService` 以连接到 Azure 租户。  
1. 输入 `get-msoldevice -deviceId <deviceId>` 。
1. 确认“已启用”设置为 True   。

## <a name="troubleshoot-your-implementation"></a>对实现进行故障排除

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

- [对 Windows 当前设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-current.md)
- [对 Windows 下层设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 门户管理设备标识](device-management-azure-portal.md)。

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
