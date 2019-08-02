---
title: 如何规划 Azure Active Directory (Azure AD) 联接实现 | Microsoft Docs
description: 介绍在环境中实现 Azure AD 联接设备的所需步骤。
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
ms.openlocfilehash: 6e58762bd5bf4342804767a200c94b432dd152a0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562217"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>如何：计划 Azure AD 加入实现

通过 Azure AD 联接，可在保持用户高效、安全的同时，也将设备直接与 Azure AD 相联接，而无需与本地 Active Directory 相联接。 Azure AD 联接面向企业大规模和大范围的部署。   

本文介绍计划 Azure AD 联接实现所需信息。
 
## <a name="prerequisites"></a>系统必备

本文假设读者已阅读 [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)。

## <a name="plan-your-implementation"></a>规划实施

若要计划 Azure AD 联接实现, 应熟悉以下内容:

|   |   |
|---|---|
|![检查][1]|查看方案|
|![检查][1]|查看标识基础结构|
|![检查][1]|评估设备管理|
|![检查][1]|了解有关应用程序和资源的注意事项|
|![检查][1]|了解预配选项|
|![检查][1]|配置企业状态漫游|
|![检查][1]|配置条件访问|

## <a name="review-your-scenarios"></a>查看方案 

尽管对于某些方案，可能首选使用混合 Azure AD 联接，但 Azure AD 联接确保能够向具有 Windows 的云优先模型进行转换。 如果想要实现设备管理的现代化并减少设备相关的 IT 成本，Azure AD 联接提供达成上述目标的绝佳基础。  
 
如果目标符合以下条件，则应考虑 Azure AD 联接：

- 正在采用 Microsoft 365 作为用户的生产力套件。
- 想要使用云设备管理解决方案管理设备。
- 想要简化地理位置分散的用户的设备预配。
- 计划实现应用程序基础结构现代化。

## <a name="review-your-identity-infrastructure"></a>查看标识基础结构  

Azure AD 联接适用于托管环境和联合环境。  

### <a name="managed-environment"></a>托管环境

可使用无缝单一登录通过[密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)或[直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

### <a name="federated-environment"></a>联合环境

联合环境应具有支持 WS-Trust 和 WS-Fed 协议的标识提供者：

- **WS-Fed：** 将设备联接到 Azure AD 时需要此协议。
- **WS-Trust：** 登录到 Azure AD 联接设备时需要此协议。 

如果标识提供者不支持这些协议，则 Azure AD 联接无法本机运行。 从 Windows 10 1809 开始，你的用户可使用基于 SAML 的标识提供者通过 [Windows 10 上的 Web 登录](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10)登录到 Azure AD 联接设备。 目前, web 登录是一项预览功能, 不建议用于生产部署。

### <a name="smartcards-and-certificate-based-authentication"></a>智能卡和基于证书的身份验证

不能使用智能卡或基于证书的身份验证将设备与 Azure AD 联接。 但是，如果配置了 AD FS，可以使用智能卡登录到 Azure AD 联接设备。

**建议：** 安装 Windows Hello 企业版，获取对 Windows 10 设备强大、无需密码的身份验证。

### <a name="user-configuration"></a>用户配置

如果在下列情况下创建用户：

- **本地 Active Directory**，需要使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 将其同步到 Azure AD。 
- **Azure AD**，无需进行其他设置。

Azure AD 加入设备不支持与 Azure AD UPN 不同的本地 UPN。 如果用户使用本地 UPN，你应计划转换成使用其 Azure AD 中的主 UPN。

## <a name="assess-your-device-management"></a>评估设备管理

### <a name="supported-devices"></a>支持的设备

Azure AD 联接：

- 仅适用于 Windows 10 设备。 
- 不适用于以前版本的 Windows 或其他操作系统。 如果有 Windows 7/8.1 设备，则必须升级到 Windows 10 以部署 Azure AD 联接。
- 在 TPM 处于 FIPS 模式下的设备上不受支持。
 
**建议：** 始终使用最新的 Windows 10 发布版本以充分利用更新的功能。

### <a name="management-platform"></a>管理平台

Azure AD 联接设备的设备管理基于 MDM 平台 (如 Intune) 和 MDM Csp。 Windows 10 提供适用于所有兼容 MDM 解决方案的内置 MDM 代理。

> [!NOTE]
> Azure AD 联接的设备中不支持组策略, 因为它们未连接到本地 Active Directory。 仅可通过 MDM 管理 Azure AD 联接的设备

管理 Azure AD 联接设备有两种方法：

- **仅限 MDM** - MDM 提供程序（例如 Intune）以独占方式托管设备。 所有策略都作为 MDM 注册过程的一部分提供。 对于 Azure AD Premium 或 EMS 客户，MDM 注册是自动步骤，此步骤是 Azure AD 联接的一部分。
- **共同管理** - MDM 提供程序和 SCCM 托管设备。 使用这种方法，在 MDM 托管设备上安装 SCCM 代理来管理某些特性。

如果要使用组策略，请使用 [MDM 迁移分析工具 (MMAT)](https://github.com/WindowsDeviceManagement/MMAT) 评估 MDM 策略奇偶校验。 

查看支持和不支持的策略，以确定是否可以使用 MDM 解决方案，而不是组策略。 对于不支持的策略，请考虑以下内容：

- Azure AD 联接设备或用户是否需要不支持的策略？
- 不支持的策略是否适用于云驱动部署？

如果 MDM 解决方案不能通过 Azure AD 应用库获取，则可以按照 [Azure Active Directory integration with MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm)（Azure Active Directory 与 MDM 集成）中概述的过程添加该解决方案。 

通过共同管理，可以使用 SCCM 来管理设备的某些特性，同时通过 MDM 平台提供策略。 Microsoft Intune 通过 SCCM 启用共同管理。 有关详细信息，请参阅[适用于 Windows 10 设备的共同管理](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview)。 如果使用除 Intune 之外的 MDM 产品，请与 MDM 提供商联系，了解适用的共同管理方案。

**建议：** 对于 Azure AD 联接设备的管理，请考虑使用“仅限 MDM 管理”这一种方法。

## <a name="understand-considerations-for-applications-and-resources"></a>了解有关应用程序和资源的注意事项

我们建议将应用程序从本地迁移到云，以获得更好的用户体验和访问控制。 但是，Azure AD 联接设备可以无缝提供到本地和云应用程序的访问权限。 有关详细信息，请参阅[本地资源的 SSO 在已加入 Azure AD 的设备上的工作原理](azuread-join-sso.md)。

以下部分列出不同类型的应用程序和资源的注意事项。

### <a name="cloud-based-applications"></a>基于云的应用程序

如果将应用程序添加到 Azure AD 应用库，则用户通过 Azure AD 联接设备获得 SSO。 无需指定其他配置。 用户在 Microsoft Edge 和 Chrome 浏览器上获取 SSO。 对于 Chrome，需要部署 [Windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 

所有 Win32 应用程序，其：

- 依赖 Web 帐户管理器 (WAM)，因为令牌请求也可获取 Azure AD 联接设备上的 SSO。 
- 不要依赖可能会提示用户进行身份验证的 WAM。 

### <a name="on-premises-web-applications"></a>本地 Web 应用

如果应用是自定义生成和/或托管在本地，需要将其添加到浏览器的受信任站点，以：

- 确保 Windows 集成身份验证运行 
- 为用户提供无提示的 SSO 体验。 

如果使用 AD FS，请参阅[使用 AD FS 验证和管理单一登录](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100))。 

**建议：** 考虑托管在云中（例如 Azure），并与 Azure AD 集成，以获得更好的体验。

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>依赖旧版协议的本地应用程序

如果设备有权访问域控制器，则用户从 Azure AD 联接设备获取 SSO。 

**建议：** 部署 [Azure AD 应用代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)，以启用这些应用程序的安全访问。

### <a name="on-premises-network-shares"></a>本地网络共享

设备有权访问本地域控制器时，你的用户从 Azure AD 联接设备获取 SSO。

### <a name="printers"></a>打印机

对于打印机，需要部署[混合云打印](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)，以查找 Azure AD 联接设备上的打印机。 

在仅限云的环境中无法自动查找打印机时，用户还可以使用打印机的 UNC 路径直接将其添加。 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>依赖计算机身份验证的本地应用程序

Azure AD 联接设备不支持依赖计算机身份验证的本地应用程序。 

**建议：** 考虑停用这些应用程序并移动到其新式替代项。

### <a name="remote-desktop-services"></a>远程桌面服务

Azure AD 联接设备的远程桌面连接需要主机是 Azure AD 联接或混合 Azure AD 联接。 不支持未联接设备或非 Windows 设备的远程桌面。 有关详细信息，请参阅 [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)（连接到远程 Azure AD 联接电脑）

## <a name="understand-your-provisioning-options"></a>了解预配选项

可以使用以下方法预配 Azure AD 联接：

- **OOBE/设置中的自助式** - 在自助模式下，用户在 Windows Out of Box Experience (OOBE) 期间或从 Windows 设置完成 Azure AD 联接过程。 有关详细信息，请参阅[将工作设备加入组织的网络](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)。 
- **Windows Autopilot** - Windows Autopilot 支持预先配置设备，可在 OOBE 中实现更顺畅的体验，以执行 Azure AD 联接。 有关详细信息，请参阅 [Windows Autopilot 概述](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)。 
- **批量注册** - 批量注册通过使用批量预配工具来配置设备，从而实现管理员驱动的 Azure AD 联接。 有关详细信息，请参阅[Windows 设备的批量注册](https://docs.microsoft.com/intune/windows-bulk-enroll)。
 
下面是这三种方法的比较 
 
|   | 自助式设置 | Windows Autopilot | 批量注册 |
| --- | --- | --- | --- |
| 需要用户交互以进行设置 | 是 | 是 | 否 |
| 需要 IT 工作量 | 否 | 是 | 是 |
| 适用流 | OOBE 和设置 | 仅限 OOBE | 仅限 OOBE |
| 主要用户的本地管理员权限 | 是，默认情况下 | 可配置性 | 否 |
| 需要设备 OEM 的支持 | 否 | 是 | 否 |
| 支持的版本 | 1511+ | 1709+ | 1703+ |
 
查看上表和以下采用任一方法的注意事项，选择一个或多个部署方法：  

- 技术精湛的用户是否自行完成设置？ 
   - 自助式最适合这些用户。 考虑使用 Windows Autopilot 来增强用户体验。  
- 用户是远程还是公司内部？ 
   - 自助式或 Autopilot 最适合远程用户进行轻松设置。 
- 你更喜欢用户驱动的配置还是管理员托管的配置？ 
   - 批量注册更适合管理员驱动的部署，以便在转交给用户之前设置设备。     
- 你是从 1-2 家 OEM 厂商处购买设备，或者有广泛的 OEM 设备经销渠道？  
   - 如果从有限的且还支持 Autopilot 的 OEM 厂商处购买，可从与 Autopilot 的紧密集成中受益。 

## <a name="configure-your-device-settings"></a>配置设备设置

通过Azure 门户可控制组织中 Azure AD 联接设备的部署。 若要配置相关设置，在“Azure Active Directory 页”中选择 `Devices > Device settings`。

### <a name="users-may-join-devices-to-azure-ad"></a>用户可将设备联接到 Azure AD

根据部署范围和你想允许设置 Azure AD 联接设备的人选，将此选项设置为“全部”或“选定”。 

![用户可将设备联接到 Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Azure AD 联接设备上的其他本地管理员

选择“选定”，并选择想要添加到所有 Azure AD 联接设备上的本地管理员组的用户。 

![Azure AD 联接设备上的其他本地管理员](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>需要进行多重身份验证才能联接设备

如果将设备联接到 Azure AD 的同时需要用户执行多重身份验证，则选择“是”。 对于使用多重身份验证将设备联接到 Azure AD 的用户，设备本身成为第 2 个因素。

![需要进行多重身份验证才能联接设备](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>配置移动性设置

可能必须首先添加 MDM 提供程序，然后才可以配置移动性设置。

**若要添加 MDM 提供程序**：

1. 在“Azure Active Directory 页”的“管理”部分，单击 `Mobility (MDM and MAM)`。 
1. 单击“添加应用程序”。
1. 从列表中选择 MDM 提供程序。

   ![添加应用程序](./media/azureadjoin-plan/04.png)

选择 MDM 提供程序配置相关设置。 

### <a name="mdm-user-scope"></a>MDM 用户范围

根据部署范围，选择“部分”或“全部”。 

![MDM 用户范围](./media/azureadjoin-plan/05.png)

根据范围，会发生以下某种情况： 

- **用户在 MDM 范围内**：如果有 Azure AD Premium 订阅，则自动进行 MDM 注册以及 Azure AD 联接。 范围内所有用户必须具有相应的 MDM 许可证。 如果此方案中 MDM 注册失败，Azure AD 联接也将失败。
- **用户不在 MDM 范围内**：如果用户不在 MDM 范围内，则无需任何 MDM 注册即可完成 Azure AD 联接。 这会产生非管理的设备。

### <a name="mdm-urls"></a>MDM URL

三个与 MDM 配置相关的 URL：

- MDM 使用条款 URL
- MDM 发现 URL 
- MDM 符合性 URL

![添加应用程序](./media/azureadjoin-plan/06.png)

每个 URL 都具有预定义的默认值。 如果这些字段都为空，请联系 MDM 提供商，获取更多详细信息。

### <a name="mam-settings"></a>MAM 设置

MAM 不适用于 Azure AD 联接。 

## <a name="configure-enterprise-state-roaming"></a>配置企业状态漫游

如果想要启用 Azure AD 的状态漫游，以便用户可以跨设备同步其设置，请参阅[在 Azure Active Directory 中启用企业状态漫游](enterprise-state-roaming-enable.md)。 

**建议**：即使对于混合 Azure AD 联接设备，也启用此设置。

## <a name="configure-conditional-access"></a>配置条件访问

如果为 Azure AD 联接设备配置的 MDM 提供程序，只要该设备处于管理状态，提供程序就将其标记为符合。 

![兼容设备](./media/azureadjoin-plan/46.png)

你可以使用此实现来[要求使用条件访问的云应用访问的托管设备](../conditional-access/require-managed-devices.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [首次运行期间使用 Azure AD 联接新的 Windows 10 设备](azuread-joined-devices-frx.md)
> [将工作设备加入组织的网络](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
