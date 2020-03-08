---
title: 为托管域配置混合 Azure Active Directory 加入 | Microsoft Docs
description: 了解如何为托管域配置混合 Azure Active Directory 加入。
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
ms.openlocfilehash: 3407214d332cbd333fe019948d254e01d71197fb
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672271"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>教程：为托管域配置混合 Azure Active Directory 加入

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 要实现此目的，可以使用下述某种方法将设备标识引入 Azure Active Directory (Azure AD) 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

将设备引入 Azure AD 可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)保护对云和本地资源的访问。

本教程介绍如何在托管环境中为已加入 Active Directory 域的计算机设备配置混合 Azure AD 加入。 

可使用[无缝单一登录](../hybrid/how-to-connect-sso.md)通过[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 或[直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md) 来部署托管环境。 这些方案不需要配置联合服务器进行身份验证。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置混合 Azure AD 联接
> * 启用 Windows 下层设备
> * 验证联接的设备
> * 故障排除

## <a name="prerequisites"></a>系统必备

本教程假定你熟悉以下文章：

- [什么是设备标识？](overview.md)
- [如何规划混合 Azure AD 加入实现](hybrid-azuread-join-plan.md)
- [如何以受控方式验证混合 Azure AD 加入](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

要配置本文中的方案，需要安装[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 或更高版本)。

验证 Azure AD Connect 是否已将要加入混合 Azure AD 的设备的计算机对象同步到 Azure AD。 如果这些计算机对象属于特定组织单位 (OU)，则还需要在 Azure AD Connect 中配置这些 OU 以进行同步。 要详细了解如何使用 Azure AD Connect 同步计算机对象，请参阅[使用 Azure AD Connect 配置筛选](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

自 1.1.819.0 版起，Azure AD Connect 包含用于配置混合 Azure AD 加入的向导。 该向导显著简化了配置过程。 向导配置设备注册的服务连接点 (SCP)。

本文中的配置步骤需要使用 Azure AD Connect 中的向导。

混合 Azure AD 加入要求设备能够从组织的网络中访问以下 Microsoft 资源：  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`（如果使用或计划使用无缝 SSO）

如果你的组织需要通过出站代理访问 Internet，Microsoft 建议[实施 Web 代理自动发现 (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10))，以使 Windows 10 计算机在 Azure AD 进行设备注册。 如果在配置和管理 WPAD 时遇到问题，请参阅[自动检测故障排除](/previous-versions/tn-archive/cc302643(v=technet.10))。 

如果不使用 WPAD 并需要在计算机上配置代理设置，则可以从 Windows 10 1709 开始。 有关详细信息，请参阅[使用组策略对象 (GPO) 配置 WinHTTP 设置](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)。

> [!NOTE]
> 如果使用 WinHTTP 设置在计算机上配置代理设置，则无法连接到所配置的代理的任何计算机将无法连接到 Internet。

如果组织需要通过经身份验证的出站代理访问 Internet，则必须确保 Windows 10 计算机能够成功验证出站代理的身份。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此必须使用计算机上下文配置出站代理身份验证。 根据配置要求使用相应的出站代理提供程序。

若要验证设备是否能够访问系统帐户下的上述 Microsoft 资源，可以使用[测试设备注册连接](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)脚本。

## <a name="configure-hybrid-azure-ad-join"></a>配置混合 Azure AD 联接

要使用 Azure AD Connect 配置混合 Azure AD 联接，需具备以下项：

- Azure AD 租户的全局管理员凭据
- 每个林的企业管理员凭据

**使用 Azure AD Connect 配置混合 Azure AD 加入：**

1. 启动 Azure AD Connect，然后选择“配置”。

   ![欢迎使用](./media/hybrid-azuread-join-managed-domains/11.png)

1. 在“其他任务”页面上，选择“配置设备选项”，然后单击“下一步”。

   ![其他任务](./media/hybrid-azuread-join-managed-domains/12.png)

1. 在“概述”页面上，选择“下一步”。

   ![概述](./media/hybrid-azuread-join-managed-domains/13.png)

1. 在“连接到 Azure AD”页上，输入 Azure AD 租户的全局管理员凭据。  

   ![连接到 Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. 在“设备选项”页面上，选择“配置混合 Azure AD 加入”，然后选择“下一步”。

   ![设备选项](./media/hybrid-azuread-join-managed-domains/15.png)

1. 在 SCP 页上，对于希望 Azure AD Connect 配置 SCP 的每个林，完成以下步骤，然后选择“下一步”：

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. 选择林。
   1. 选择身份验证服务。
   1. 单击“添加”，输入企业管理员凭据。

1. 在“设备操作系统”页面上，选择 Active Directory 环境中设备使用的操作系统，然后选择“下一步”。

   ![设备操作系统](./media/hybrid-azuread-join-managed-domains/17.png)

1. 在“准备好配置”页面上，选择“配置”。

   ![已准备好配置](./media/hybrid-azuread-join-managed-domains/19.png)

1. 在“配置完成”页面上，选择“退出”。

   ![配置完成](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则需要：

- 配置设备注册的本地 Intranet 设置
- 配置无缝 SSO
- 安装适用于 Windows 下层计算机的 Microsoft Workplace Join

> [!NOTE]
> Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，[对 Windows 7 的支持已结束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>配置设备注册的本地 Intranet 设置

要成功完成 Windows 下层设备的混合 Azure AD 加入，同时避免在设备向 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，从而在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

此外，还需要在用户的本地 Intranet 区域中启用“允许通过脚本更新状态栏”。

### <a name="configure-seamless-sso"></a>配置无缝 SSO

若要在使用 [PHS](../hybrid/whatis-phs.md) 或 [PTA](../hybrid/how-to-connect-pta.md) 作为 Azure AD 云身份验证方法的托管域中成功完成 Windows 下层设备的混合 Azure AD 联接，还必须[配置无缝 SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>安装适用于 Windows 下层计算机的 Microsoft Workplace Join

要注册 Windows 下层设备，组织必须安装[适用于 Windows 10 计算机的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 适用于 Windows 10 计算机的 Microsoft Workplace Join 在 Microsoft 下载中心提供。

可以使用  [Microsoft Endpoint Configuration Manager](/configmgr/) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 `quiet` 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 在 Azure AD 中进行身份验证后，此任务便会使用用户凭据将设备静默加入 Azure AD。

## <a name="verify-the-registration"></a>验证注册

要验证 Azure 租户中的设备注册状态，可使用 [Azure Active Directory PowerShell 模块](/powershell/azure/install-msonlinev1?view=azureadps-2.0)中的 [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) cmdlet。

使用 Get-MSolDevice cmdlet 检查服务详细信息时：

- 必须存在其**设备 ID** 与 Windows 客户端上的 ID 相匹配的对象。
- DeviceTrustType 的值必须是“已加入域”。 此设置相当于 Azure AD 门户中“设备”页上的“已加入混合 Azure AD”状态。
- 对于采用了条件访问的设备，“已启用”值必须为 True，“DeviceTrustLevel”值必须为“Managed”。

**检查服务详细信息**：

1. 以管理员身份打开 Windows PowerShell。
1. 输入 `Connect-MsolService` 以连接到 Azure 租户。  
1. 输入 `get-msoldevice -deviceId <deviceId>`。
1. 确认“已启用”设置为 True。

## <a name="troubleshoot-your-implementation"></a>对实现进行故障排除

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

- [对 Windows 当前设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-current.md)
- [对 Windows 下层设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 门户管理设备标识](device-management-azure-portal.md)。
