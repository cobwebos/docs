---
title: 为联盟域配置混合 Azure Active Directory 加入 | Microsoft Docs
description: 了解如何为联盟域配置混合 Azure Active Directory 加入。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a37353615e35cd75c126c268de71d10077a9071
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268428"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>教程：为联盟域配置混合 Azure Active Directory 加入

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 要实现此目的，可以使用下述某种方法将设备标识引入 Azure Active Directory (Azure AD) 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

将设备引入 Azure AD 可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../conditional-access/howto-conditional-access-policy-compliant-device.md)保护对云和本地资源的访问。

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **WIAORMULTIAUTHN 声明：** 此声明是为 Windows 下层设备执行混合Azure AD 加入所必需的。
- **WS-Trust 协议：** 使用 Azure AD 对当前已加入混合 Azure AD 的 Windows 设备进行身份验证时需要此协议。
  使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 和 **adfs/services/trust/13/windowstransport** 应仅作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 可以通过 AD FS 管理控制台中的“服务” > “终结点”查看已启用哪些终结点。 

本教程介绍如何使用 AD FS 在联合环境中为已加入 Active Directory 域的计算机设备配置混合 Azure AD 加入。

学习如何：

> [!div class="checklist"]
> * 配置混合 Azure AD 联接
> * 启用 Windows 下层设备
> * 验证注册
> * 疑难解答

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

> [!WARNING]
> 如果组织使用针对数据丢失防护或 Azure AD 租户限制等方案拦截 SSL 流量的代理服务器，请确保在 TLS 中断和检查中排除发往“https://device.login.microsoftonline.com”的流量。 未能排除“https://device.login.microsoftonline.com”可能会导致干扰客户端证书身份验证，从而导致设备注册和基于设备的条件访问出现问题。

从 Windows 10 1803 开始，如果无法在使用 AD FS 的联合环境中实现即时混合 Azure AD 加入，我们将依赖 Azure AD Connect 同步 Azure AD 中的计算机对象，该计算机对象随后用于完成混合 Azure AD 加入的设备注册。 验证 Azure AD Connect 是否已将要加入混合 Azure AD 的设备的计算机对象同步到 Azure AD。 如果这些计算机对象属于特定组织单位 (OU)，则还需要在 Azure AD Connect 中配置这些 OU 以进行同步。 要详细了解如何使用 Azure AD Connect 同步计算机对象，请参阅[使用 Azure AD Connect 配置筛选](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

如果你的组织需要通过出站代理访问 Internet，Microsoft 建议[实施 Web 代理自动发现 (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10))，以使 Windows 10 计算机在 Azure AD 进行设备注册。 如果在配置和管理 WPAD 时遇到问题，请参阅[自动检测故障排除](/previous-versions/tn-archive/cc302643(v=technet.10))。 

如果不使用 WPAD 并希望在计算机上配置代理设置，则可以从 Windows 10 1709 开始。 有关详细信息，请参阅[使用组策略对象 (GPO) 配置 WinHTTP 设置](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)。

> [!NOTE]
> 如果使用 WinHTTP 设置在计算机上配置代理设置，则无法连接到所配置的代理的任何计算机将无法连接到 Internet。

如果组织需要通过经身份验证的出站代理访问 Internet，则必须确保 Windows 10 计算机能够成功验证出站代理的身份。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此必须使用计算机上下文配置出站代理身份验证。 根据配置要求使用相应的出站代理提供程序。

若要验证设备是否能够访问系统帐户下的上述 Microsoft 资源，可以使用[测试设备注册连接](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)脚本。

## <a name="configure-hybrid-azure-ad-join"></a>配置混合 Azure AD 联接

要使用 Azure AD Connect 配置混合 Azure AD 加入，需具备以下各项：

- Azure AD 租户的全局管理员凭据  
- 每个林的企业管理员凭据
- AD FS 管理员凭据

**使用 Azure AD Connect 配置混合 Azure AD 加入**：

1. 启动 Azure AD Connect，然后选择“配置”。

   ![欢迎使用](./media/hybrid-azuread-join-federated-domains/11.png)

1. 在“其他任务”页面上，选择“配置设备选项”，然后单击“下一步”  。

   ![其他任务](./media/hybrid-azuread-join-federated-domains/12.png)

1. 在“概述”页面上，选择“下一步” 。

   ![概述](./media/hybrid-azuread-join-federated-domains/13.png)

1. 上“连接到 Azure AD”页面上，输入 Azure AD 租户的全局管理员凭据，然后选择“下一步” 。

   ![连接到 Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. 在“设备选项”页面上，选择“配置混合 Azure AD 加入”，然后选择“下一步”  。

   ![设备选项](./media/hybrid-azuread-join-federated-domains/15.png)

1. 在“SCP”页面上，完成以下步骤，然后选择“下一步” ：

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. 选择林。
   1. 选择身份验证服务。 除非你的组织仅有 Windows 10 客户端且你已配置计算机/设备同步或者你的组织使用无缝 SSO，否则必须选择“AD FS 服务器”。
   1. 单击“添加”，输入企业管理员凭据。

1. 在“设备操作系统”页面上，选择 Active Directory 环境中设备使用的操作系统，然后选择“下一步” 。

   ![设备操作系统](./media/hybrid-azuread-join-federated-domains/17.png)

1. 在“联合身份验证配置”页面上，输入 AD FS 管理员凭据，然后选择“下一步” 。

   ![联合身份验证配置](./media/hybrid-azuread-join-federated-domains/18.png)

1. 在“准备好配置”页面上，选择“配置” 。

   ![已准备好配置](./media/hybrid-azuread-join-federated-domains/19.png)

1. 在“配置完成”页面上，选择“退出” 。

   ![配置完成](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则需要：

- 配置设备注册的本地 Intranet 设置
- 安装适用于 Windows 下层计算机的 Microsoft Workplace Join

> [!NOTE]
> Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，[对 Windows 7 的支持已结束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>配置设备注册的本地 Intranet 设置

要成功完成 Windows 下层设备的混合 Azure AD 加入，同时避免在设备向 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，从而在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

- `https://device.login.microsoftonline.com`
- 组织的 STS（联合域）
- `https://autologon.microsoftazuread-sso.com`（适用于无缝 SSO）

此外，还需要在用户的本地 Intranet 区域中启用“允许通过脚本更新状态栏”。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>安装适用于 Windows 下层计算机的 Microsoft Workplace Join

要注册 Windows 下层设备，组织必须安装[适用于 Windows 10 计算机的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 适用于 Windows 10 计算机的 Microsoft Workplace Join 在 Microsoft 下载中心提供。

可以使用  [Microsoft Endpoint Configuration Manager](/configmgr/) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 `quiet` 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

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
3. 如果“已注册”列显示“挂起”，则表明混合 Azure AD 联接尚未完成。 在联合环境中，只有当注册失败并且 AAD Connect 配置为同步设备时，才会发生这种情况。
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

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

- [使用 dsregcmd 命令排查设备问题](./troubleshoot-device-dsregcmd.md)
- [对 Windows 当前设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-current.md)
- [对 Windows 下层设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 门户管理设备标识](device-management-azure-portal.md)。

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png