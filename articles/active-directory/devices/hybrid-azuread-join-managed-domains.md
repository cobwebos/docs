---
title: 为托管域配置混合 Azure Active Directory 加入 | Microsoft Docs
description: 了解如何为托管域配置混合 Azure Active Directory 加入。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56b0685dee518399ae8328ddac18f03e82918a38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268411"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>教程：为托管域配置混合 Azure Active Directory 加入

本教程介绍如何为已加入 Active Directory 域的设备配置混合 Azure Active Directory (Azure AD) 加入。 此方法支持同时包含本地 Active Directory 和 Azure AD 的托管环境。

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 可以通过管理 Azure AD 中的设备标识来实现此目的。 使用下列方法之一：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

本文重点介绍混合 Azure AD 加入。

将设备引入 Azure AD 可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../conditional-access/howto-conditional-access-policy-compliant-device.md)保护对云和本地资源的访问。

可以使用支持[无缝单一登录](../hybrid/how-to-connect-sso.md)的[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 或[直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md) 来部署托管环境。 这些方案不需要配置联合服务器进行身份验证。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置混合 Azure AD 联接
> * 启用 Windows 下层设备
> * 验证联接的设备
> * 疑难解答

## <a name="prerequisites"></a>先决条件

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)（1.1.819.0 或更高版本）
- Azure AD 租户的全局管理员凭据
- 每个林的企业管理员凭据

通过以下文章熟悉相关知识：

- [什么是设备标识？](overview.md)
- [如何：规划混合 Azure Active Directory 加入的实施](hybrid-azuread-join-plan.md)
- [以受控方式验证混合 Azure AD 加入](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

验证 Azure AD Connect 是否已将要加入混合 Azure AD 的设备的计算机对象同步到 Azure AD。 如果这些计算机对象属于特定的组织单位 (OU)，请将 OU 配置为在 Azure AD Connect 中进行同步。 若要详细了解如何使用 Azure AD Connect 同步计算机对象，请参阅[基于组织单位的筛选](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

从版本 1.1.819.0 开始，Azure AD Connect 包含用于配置混合 Azure AD 加入的向导。 该向导显著简化了配置过程。 向导配置设备注册的服务连接点 (SCP)。

本文中的配置步骤需要使用 Azure AD Connect 中的向导。

混合 Azure AD 加入要求设备能够从组织的网络中访问以下 Microsoft 资源：  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`（如果使用或计划使用无缝 SSO）

> [!WARNING]
> 如果组织使用针对数据丢失防护或 Azure AD 租户限制等方案拦截 SSL 流量的代理服务器，请确保在 TLS 中断和检查中排除发往“https://device.login.microsoftonline.com”的流量。 未能排除“https://device.login.microsoftonline.com”可能会导致干扰客户端证书身份验证，从而导致设备注册和基于设备的条件访问出现问题。

如果你的组织需要通过出站代理访问 Internet，你可使用[实施 Web 代理自动发现 (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10))，使 Windows 10 计算机能够在 Azure AD 中进行设备注册。 若要解决在配置和管理 WPAD 时遇到问题，请参阅[排查自动检测的问题](/previous-versions/tn-archive/cc302643(v=technet.10))。 在 1709 更新之前的 Windows 10 设备中，只能使用 WPAD 来配置代理以使用混合 Azure AD 联接。 

从 Windows 10 1709 开始，如果不使用 WPAD，则可以在计算机上配置 WinHTTP 代理设置。 有关详细信息，请参阅 [GPO 部署的 WinHTTP 代理设置](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)。

> [!NOTE]
> 如果使用 WinHTTP 设置在计算机上配置代理设置，则无法连接到所配置的代理的任何计算机将无法连接到 Internet。

如果组织需要通过经身份验证的出站代理访问 Internet，请确保 Windows 10 计算机能够成功对出站代理进行身份验证。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此请使用计算机上下文配置出站代理身份验证。 根据配置要求使用相应的出站代理提供程序。

使用[测试设备注册连接](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)脚本验证设备是否能够访问系统帐户下的上述 Microsoft 资源。

## <a name="configure-hybrid-azure-ad-join"></a>配置混合 Azure AD 联接

若要使用 Azure AD Connect 配置混合 Azure AD 加入：

1. 启动 Azure AD Connect，然后选择“配置”。

   ![欢迎使用](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. 在“其他任务”中，依次选择“配置设备选项”、“下一步”。  

   ![其他任务](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. 在“概述”中选择“下一步”。 

   ![概述](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. 在“连接到 Azure AD”中，输入 Azure AD 租户的全局管理员凭据。  

   ![连接到 Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. 在“设备选项”中，依次选择“配置混合 Azure AD 加入”、“下一步”。  

   ![设备选项](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. 在“SCP 配置”中，对于你希望 Azure AD Connect 在其中配置 SCP 的每个林，请完成以下步骤，然后选择“下一步”。 

   1. 选择“林”。
   1. 选择“身份验证服务”。
   1. 单击“添加”，输入企业管理员凭据。

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. 在“设备操作系统”中选择 Active Directory 环境中设备使用的操作系统，然后选择“下一步”。 

   ![设备操作系统](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. 在“已准备好配置”中选择“配置”。 

   ![已准备好配置](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. 在“配置完成”中选择“退出”。 

   ![配置完成](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则必须：

- 配置设备注册的本地 Intranet 设置
- 配置无缝 SSO
- 在 Windows 下层计算机上安装 Microsoft 工作区加入

> [!NOTE]
> Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，请参阅[已终止对 Windows 7 的支持](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>配置设备注册的本地 Intranet 设置

若要完成 Windows 下层设备的混合 Azure AD 加入，同时避免在设备向 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，以在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

此外，必须在用户的本地 Intranet 区域中启用“允许通过脚本更新状态栏”。

### <a name="configure-seamless-sso"></a>配置无缝 SSO

若要在使用[密码哈希同步](../hybrid/whatis-phs.md)或[直通身份验证](../hybrid/how-to-connect-pta.md)作为 Azure AD 云身份验证方法的托管域中完成 Windows 下层设备的混合 Azure AD 加入，还必须[配置无缝 SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>在 Windows 下层计算机上安装 Microsoft 工作区加入

若要注册 Windows 下层设备，组织必须安装[适用于非 Windows 10 计算机的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 适用于 Windows 10 计算机的 Microsoft Workplace Join 在 Microsoft 下载中心提供。

可以使用  [Microsoft Endpoint Configuration Manager](/configmgr/) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 `quiet` 参数）。 最新版本的 Configuration Manager 比旧版更具优势，例如，它可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 在 Azure AD 中进行身份验证后，此任务便会使用用户凭据将设备静默加入 Azure AD。

## <a name="verify-the-registration"></a>验证注册

可通过以下三种方法查找和验证设备状态：

### <a name="locally-on-the-device"></a>在设备本地

1. 打开 Windows PowerShell。
2. 输入 `dsregcmd /status`。
3. 确保“AzureAdJoined”和“DomainJoined”均设置为“是”。
4. 可以使用 DeviceId，并使用 Azure 门户或 PowerShell 比较服务的状态。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

1. 使用[直接链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)进入设备页面。
2. 有关如何查找设备的信息，请参阅[如何使用 Azure 门户管理设备标识](./device-management-azure-portal.md)。
3. 如果“已注册”列显示“挂起”，则表明混合 Azure AD 联接尚未完成。
4. 如果“已注册”列包含日期/时间，则表明混合 Azure AD 联接已完成。

### <a name="using-powershell"></a>使用 PowerShell

使用 **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** 验证 Azure 租户中的设备注册状态。 [Azure Active Directory PowerShell 模块](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-2.0)中包含此 cmdlet。

使用 Get-MSolDevice cmdlet 检查服务详细信息时：

- 必须存在其**设备 ID** 与 Windows 客户端上的 ID 相匹配的对象。
- **DeviceTrustType** 的值为 **Domain Joined**。 此设置相当于 Azure AD 门户中“设备”页上的“已加入混合 Azure AD”状态 。
- 对于条件访问中使用的设备，**Enabled** 的值为 **True**，**DeviceTrustLevel** 的值为 **Managed**。

1. 以管理员身份打开 Windows PowerShell。
2. 输入 `Connect-MsolService` 以连接到 Azure 租户。

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>统计所有已加入混合 Azure AD 的设备（不包括“挂起”状态）

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>统计所有已加入混合 Azure AD 并处于“挂起”状态的设备

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>列出所有已加入混合 Azure AD 的设备

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>列出所有已加入混合 Azure AD 并处于“挂起”状态的设备

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>列出单个设备的详细信息：

1. 输入 `get-msoldevice -deviceId <deviceId>`（这是在设备本地获取的 DeviceId）。
2. 确认“已启用”设置为 True 。

## <a name="troubleshoot-your-implementation"></a>对实现进行故障排除

如果在对已加入域的 Windows 设备完成混合 Azure AD 加入时遇到问题，请参阅：

- [使用 dsregcmd 命令排查设备问题](./troubleshoot-device-dsregcmd.md)
- [排查已加入混合 Azure Active Directory 的设备的问题](troubleshoot-hybrid-join-windows-current.md)
- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章，了解如何使用 Azure 门户管理设备标识。
> [!div class="nextstepaction"]
> [管理设备标识](device-management-azure-portal.md)