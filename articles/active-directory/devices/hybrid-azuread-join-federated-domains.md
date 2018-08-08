---
title: 如何配置联接到混合 Azure Active Directory 的设备 | Microsoft Docs
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 1aa5c0f259fdb5fce449bc86f9cf293801b4770a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368665"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>教程：为联盟域配置混合 Azure Active Directory 联接

与用户类似，设备将成为要保护的另一标识，并且也用于随时保护资源和位置。 为此，可通过下述某一方法将设备的标识引入 Azure AD：

- Azure AD 联接
- 混合 Azure AD 联接
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../active-directory-conditional-access-azure-portal.md)保护对云和本地资源的访问。

本教程将介绍如何为使用 ADFS 进行联盟的设备配置混合 Azure AD 联接。

> [!div class="checklist"]
> * 配置混合 Azure AD 联接
> * 启用 Windows 下层设备
> * 验证注册
> * 故障排除


## <a name="prerequisites"></a>先决条件

本教程假定你熟悉以下内容：

-  [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)

-  [如何计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)



要配置本教程中的方案，需具备以下项：

- 带 AD FS 的 Windows Server 2012 R2

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 或更高版本。 
 

自版本 1.1.819.0 起，Azure AD Connect 提供了混合 Azure AD 联接的配置向导。 该向导可让你显著简化配置过程。 相关向导：

- 配置设备注册的服务连接点 (SCP)

- 备份现有的 Azure AD 信赖方信任

- 更新 Azure AD 信任中的声明规则

本文中的配置步骤基于此向导。 如果安装有较旧版本的 Azure AD Connect，需要将其升级到 1.1.819 或更高版本。 如果无法安装最新版本的 Azure AD Connect，请参阅[如何手动配置设备注册](../device-management-hybrid-azuread-joined-devices-setup.md)。

要实现混合 Azure AD 联接，设备必须有权从组织的网络中访问以下 Microsoft 资源：  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- 组织的 STS（联盟域）
- https://autologon.microsoftazuread-sso.com（如果正在使用或计划使用无缝 SSO）

自 Windows 10 1709 起，如果组织需要通过出站代理访问 Internet，可使用组策略对象 (GPO) 在计算机上配置代理设置。 如果计算机运行的版本比 Windows 10 1709 低，必须实现 Web 代理自动发现 (WPAD)，让 Windows 10 能够向 Azure AD 注册设备。 

如果组织需要通过经身份验证的出站代理访问 Internet，则必须确保 Windows 10 计算机能够成功验证出站代理的身份。 由于 Windows 10 计算机使用计算机上下文运行设备注册，因此必须使用计算机上下文配置出站代理身份验证。 根据配置要求使用相应的出站代理提供程序。 


## <a name="configure-hybrid-azure-ad-join"></a>配置混合 Azure AD 联接

要使用 Azure AD Connect 配置混合 Azure AD 联接，需具备以下项：

- Azure AD 租户的全局管理员凭据。  

- 每个林的企业管理员凭据。

- AD FS 管理员凭据。 


使用 Azure AD Connect 配置混合 Azure AD 联接：

1. 启动 Azure AD Connect，然后单击“配置”。

    ![欢迎使用](./media/hybrid-azuread-join-federated-domains/11.png)

2. 在“其他任务”页上，选择“配置设备选项”，然后单击“下一步”。 

    ![其他任务](./media/hybrid-azuread-join-federated-domains/12.png)

3. 在“概述”页上，单击“下一步”。 

    ![概述](./media/hybrid-azuread-join-federated-domains/13.png)

4. 在“连接到 Azure AD”页上，输入 Azure AD 租户的全局管理员凭据，然后单击“下一步”。   

    ![连接到 Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. 在“设备选项”页上，选择“配置混合 Azure AD 联接”，然后单击“下一步”。 

    ![设备选项](./media/hybrid-azuread-join-federated-domains/15.png)

6. 在“SCP”页上，执行以下步骤，并单击“下一步”： 

    ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

    a. 选择林。

    b. 选择身份验证服务。

    c. 单击“添加”，输入企业管理员凭据。


7. 在“设备操作系统”页上，选择 Active Directory 环境中设备使用的操作系统，然后单击“下一步”。 

    ![设备操作系统](./media/hybrid-azuread-join-federated-domains/17.png)

8. 在“联合身份验证配置”页上，输入 AD FS 管理员凭据，然后单击“下一步”。 

    ![联合身份验证配置](./media/hybrid-azuread-join-federated-domains/18.png)

9. 在“准备好配置”页上，单击“配置”。 

    ![已准备好配置](./media/hybrid-azuread-join-federated-domains/19.png)

10. 在“配置完成”页上，单击“退出”。 

    ![配置完成](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则需要：

- 更新设备设置
 
- 配置设备注册的本地 Intranet 设置


### <a name="update-device-settings"></a>更新设备设置 

要注册 Windows 下层设备，需确保已设置允许用户在 Azure AD 中注册设备的设备设置。 可在 Azure 门户的以下位置找到此设置：

`Home > [Name of your tenant] > Devices - Device settings`  


    
必须将以下策略设置为“所有”：“用户可以向 Azure AD 注册其设备”

![注册设备](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>配置设备注册的本地 Intranet 设置

要成功完成 Windows 下层设备的混合 Azure AD 联接，同时避免在设备对 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，从而在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- 组织的安全令牌服务（STS - 联盟域）

- `https://autologon.microsoftazuread-sso.com`（适用于无缝 SSO）。

此外，还需要在用户的本地 Intranet 区域中启用“允许通过脚本更新状态栏”。



## <a name="verify-the-registration"></a>验证注册

要验证 Azure 租户中的设备注册状态，可使用 [Azure Active Directory PowerShell 模块](/powershell/azure/install-msonlinev1?view=azureadps-2.0)中的 [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet。

在使用 Get-MSolDevice cmdlet 检查服务详细信息时：

- 必须存在其设备 ID 与 Windows 客户端上的 ID 相匹配的对象。
- DeviceTrustType 的值必须是“已加入域”。 这相当于 Azure AD 门户中“设备”页上的“已加入混合 Azure AD”状态。
- 对于采用了条件访问的设备，“已启用”值必须为 True。 


检查服务详细信息：

1. 以管理员身份打开 Windows PowerShell。

2. 键入 `Connect-MsolService` 以连接到 Azure 租户。  

3. 键入 `get-msoldevice -deviceId <deviceId>`。

6. 确认“已启用”设置为 True。





## <a name="troubleshoot-your-implementation"></a>对实现进行故障排除

如果在完成已加入域的 Windows 设备的混合 Azure AD 联接方面遇到问题，请参阅：

- [对 Windows 当前设备的混合 Azure AD 联接进行故障排除](../device-management-troubleshoot-hybrid-join-windows-current.md)
- [对 Windows 下层设备的混合 Azure AD 联接进行故障排除](../device-management-troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为托管域配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)
> [手动配置混合 Azure Active Directory 联接](../device-management-hybrid-azuread-joined-devices-setup.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
