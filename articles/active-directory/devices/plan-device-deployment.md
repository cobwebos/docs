---
title: 规划 Azure Active Directory 设备部署
description: 选择满足组织需求 Azure AD 设备集成策略。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf30570f10f176c47aed0f99127e03a027eff775
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093083"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>规划 Azure Active Directory 设备部署

本文将帮助你评估将设备与 Azure AD 集成的方法、选择实现计划，并提供指向受支持的设备管理工具的关键链接。

用户登录所采用的设备的横向扩展。 组织可以提供台式计算机、便携式计算机、手机、平板电脑和其他设备。 你的用户可能会引入自己的设备阵列，并从不同的位置访问信息。 在此环境中，你作为管理员的作业是使你的组织资源在所有设备上保持安全。

Azure Active Directory (Azure AD) 使你的组织能够通过设备标识管理实现这些目标。 你现在可以在 Azure AD 中获取设备，并从 [Azure 门户](https://portal.azure.com/)中的一个中心位置控制它们。 这为你提供了统一的体验和增强的安全性，并减少了配置新设备所需的时间。

可以通过多种方法将设备集成到 Azure AD 中：

* 可以将 [设备注册](concept-azure-ad-register.md) 到 Azure AD

* 将[设备加入](concept-azure-ad-join.md)到 Azure AD (仅限云的) 或

* 在本地 Active Directory 中的设备与 Azure AD 之间[创建混合 Azure AD 加入](concept-azure-ad-join-hybrid.md)。 

## <a name="learn"></a>Learn

在开始之前，请确保熟悉 [设备标识管理概述](overview.md)。

### <a name="benefits"></a>优点

为设备提供 Azure AD 标识的主要好处是：

* 提高工作效率-通过 Azure AD，你的用户可以对本地资源和云资源进行 [无缝登录 (SSO) ](./azuread-join-sso.md) ，使其在任何位置都能高效工作。

* 提高安全性– Azure AD 设备使你能够基于设备或用户的身份将 [ (CA) 策略的条件访问](../conditional-access/require-managed-devices.md) 应用于资源。 CA 策略可以使用 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)提供额外的保护。 将设备加入 Azure AD 是使用 [无密码 Authentication](../authentication/concept-authentication-passwordless.md) 策略提高安全性的先决条件。

* 提高用户体验–在 Azure AD 中使用设备标识，你可以让用户轻松地从个人和公司设备访问组织的基于云的资源。 管理员可以为所有 Windows 设备上的统一体验启用 [企业状态漫游](enterprise-state-roaming-overview.md) 。

* 简化部署和管理–设备标识管理简化了将设备与 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)、 [批量预配](/mem/intune/enrollment/windows-bulk-enroll)和自助服务一起 Azure AD 的过程 [： (OOBE) 的全新体验 ](../user-help/user-help-join-device-on-network.md)。 你可以通过移动设备管理来管理这些设备 (MDM) 工具，如 [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)，以及其在 [Azure 门户](https://portal.azure.com/)中的标识。

### <a name="training-resources"></a>培训资源

视频：  [使用设备控件的条件性访问](https://youtu.be/NcONUf-jeS4)

常见问题解答： [Azure AD 设备管理常见问题](faq.md)  、 [设置和数据漫游常见问题](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑组织的需求。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，失败的原因往往是对影响、结果和责任的预期不符。 若要避免这些问题，请 [确保你正在吸引正确的利益干系人](../fundamentals/active-directory-deployment-plans.md) ，并且项目中的利益干系人角色非常了解。 

对于此计划，向列表中添加以下利益干系人：

| 角色| 说明 |
| - | - |
| 设备管理员| 设备团队的代表，可以验证计划是否满足组织的设备要求。 |
| 网络管理员| 网络团队中可确保满足网络要求的代表。 |
| 设备管理团队| 管理设备清单的团队。 |
| 特定于 OS 的管理团队| 支持和管理特定 OS 版本的团队。 例如，可能有一个具有针对性的 Mac 团队。 |

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

建议在测试环境中或在一小组测试设备上使用集成方法的初始配置。 请参阅[试点的最佳做法](../fundamentals/active-directory-deployment-plans.md)。

混合 Azure AD 联接部署非常简单，并且在没有必要最终用户操作的情况下，100% 的管理员任务。 在整个组织中同时启用 [混合 Azure AD 联接](hybrid-azuread-join-control.md) 之前，您可能需要对其进行控制验证。

## <a name="choose-your-integration-methods"></a>选择集成方法

你的组织可以在一个 Azure AD 租户中使用多个设备集成方法。 目标是选择方法 () 适用于在 Azure AD 中安全管理设备。 可以通过许多参数来推动此决定，包括所有权、设备类型、主要受众和组织的基础结构。

以下信息可帮助你决定要使用的集成方法。

### <a name="decision-tree-for-devices-integration"></a>设备集成决策树

使用此树来确定组织拥有的设备的选项。 

> [!NOTE]
> 此关系图中未显示个人或自带设备 (BYOD) 方案。 它们始终导致 Azure AD 的注册。

 ![决策树](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>比较矩阵

iOS 和 Android 设备只能注册 Azure AD。 下表提供了有关 Windows 客户端设备的高级注意事项。 将其用作概述，并详细了解不同的集成方法。

| 注意事项 | 已注册 Azure AD| Azure AD 加入| 混合 Azure AD 加入 |
| - | - | - | - |
| **客户端操作系统**| | |  |
| Windows 10 设备| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| Windows 下层设备 (Windows 8.1 或 Windows 7) | | | ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
|**登录选项**| | |  |
| 最终用户本地凭据| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| |  |
| Password| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| 设备 PIN| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello for Business| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| FIDO 2.0 安全密钥| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 应用 (无密码) | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
|**关键功能**| | |  |
| SSO 到云资源| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| SSO 到本地资源| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| 条件性访问 <br>  (要求设备标记为符合)  <br>  (必须由 MDM 管理) | ![这些值的选中标记。](./media/plan-device-deployment/check.png) | ![这些值的选中标记。](./media/plan-device-deployment/check.png)|![这些值的选中标记。](./media/plan-device-deployment/check.png) |
条件性访问 <br> (要求已加入混合 Azure AD 设备) | | | ![这些值的选中标记。](./media/plan-device-deployment/check.png)
| Windows 登录屏幕上的自助密码重置| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| Windows hello PIN 重置| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |
| 跨设备的企业状态漫游| | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 注册 

已注册的设备通常通过 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)进行管理。 设备在 Intune 中注册的方式有很多，具体取决于操作系统。 

Azure AD 注册的设备支持自带设备 (BYOD) 和公司拥有的设备到 SSO 到云资源。 资源的访问基于应用到设备和用户的 Azure AD [CA 策略](../conditional-access/require-managed-devices.md) 。

### <a name="registering-devices"></a>注册设备

已注册的设备通常通过 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)进行管理。 设备在 Intune 中注册的方式有很多，具体取决于操作系统。 

安装公司门户应用的用户注册了 BYOD 和公司拥有的移动设备。

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

如果注册设备是组织的最佳选择，请参阅以下资源：

* 此 [Azure AD 已注册设备](concept-azure-ad-register.md)的概述。

* 本最终用户文档介绍了如何在 [组织的网络上注册您的个人设备](../user-help/user-help-register-device-on-network.md)。

## <a name="azure-ad-join"></a>Azure AD 加入

Azure AD 联接使你可以使用 Windows 过渡到云优先模型。 如果打算实现设备管理的现代化并降低与设备相关的 IT 成本，则它提供了一个绝佳的基础。 Azure AD 联接仅适用于 Windows 10 设备。 将其视为新设备的第一个选项。

但是，Azure AD 联接的设备在组织的网络中时， [可以通过 SSO 连接到本地资源](azuread-join-sso.md) ，可以向本地服务器（如文件、打印和其他应用程序）进行身份验证。

如果这是你的组织的最佳选择，请参阅以下资源：

* 此 [Azure AD 联接设备](concept-azure-ad-join.md)的概述。

* 熟悉 [Azure AD 联接实现计划](azureadjoin-plan.md)。

### <a name="provisioning-azure-ad-join-to-your-devices"></a>将 Azure AD 加入到设备

若要预配 Azure AD 联接，可使用以下方法：

* 自助服务： [Windows 10 首次运行体验](azuread-joined-devices-frx.md)

如果设备安装了 Windows 10 专业版或 Windows 10 企业版，则体验将默认为公司所拥有设备的设置过程。

* [Windows 全新体验 (OOBE) 或 Windows 设置](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [批量注册](/mem/intune/enrollment/windows-bulk-enroll)

仔细 [比较这些方法](azureadjoin-plan.md)后，选择部署过程。

你可能确定 Azure AD 联接是设备的最佳解决方案，并且该设备可能已经处于不同的状态。 下面是升级注意事项。

| 当前设备状态| Desired 设备状态| 操作说明 |
| - | - | - |
| 已加入本地域| Azure AD 加入| 在加入到 Azure AD 之前从本地域中脱离设备 |
| 混合 Azure AD 联接| Azure AD 加入| 在加入到 Azure AD 之前，从本地域和 Azure AD 中脱离设备 |
| 已注册 Azure AD| Azure AD 加入| 在加入到 Azure AD 之前取消注册设备 |


## <a name="hybrid-azure-ad-join"></a>混合 Azure AD 加入

如果你有本地 Active Directory 环境，并且想要将已加入 Active Directory 域的计算机加入到 Azure AD 中，则可以通过混合 Azure AD 加入来实现此目的。 它支持 [范围广泛的 windows 设备](hybrid-azuread-join-plan.md)，包括 windows 当前和 windows 下层设备。

大多数组织已加入域的设备，并通过组策略或 System Center Configuration Manager (SCCM) 对其进行管理。 在这种情况下，我们建议将混合 Azure AD 联接配置为开始获得权益，同时利用现有投资。

如果混合 Azure AD 联接是你的组织的最佳选择，请参阅以下资源：

* 此 [混合 Azure AD 联接设备](concept-azure-ad-join-hybrid.md)的概述。

* 熟悉 [混合 Azure AD 联接实现](hybrid-azuread-join-plan.md) 计划。

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>设置混合 Azure AD 加入到设备

[查看标识基础结构](hybrid-azuread-join-plan.md)。 Azure AD Connect 提供了一个向导，用于为以下内容配置混合 Azure AD Join：

* [联合域](hybrid-azuread-join-federated-domains.md)

* [托管域](hybrid-azuread-join-managed-domains.md)

如果安装所需版本的 Azure AD Connect 不是你的选择，请参阅 [如何手动配置混合 Azure AD 加入](hybrid-azuread-join-manual.md)。 

> [!NOTE] 
> 默认情况下，已加入域的 Windows 10 设备尝试自动加入到 Azure AD，以混合 Azure AD 加入。 仅当必备设置正确的环境时，此设置才会成功。 

你可能确定混合 Azure AD 联接是设备的最佳解决方案，并且该设备可能已经处于不同的状态。 下面是升级注意事项。

| 当前设备状态| Desired 设备状态| 操作说明 |
| - | - | - |
| 本地域加入| 混合 Azure AD 联接| 使用 Azure AD 连接或 AD FS 加入 Azure |
| 已加入本地工作组或新工作组| 混合 Azure AD 联接| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)支持。 否则，设备需要加入本地域，才能混合 Azure AD 联接 |
| 已加入 Azure AD| 混合 Azure AD 联接| 从 Azure AD 中脱离，使其进入本地工作组或新状态。 |
| Azure AD 已注册| 混合 Azure AD 联接| 取决于 Windows 版本。 [请参阅这些注意事项](hybrid-azuread-join-plan.md)。 |

## <a name="manage-your-devices"></a>管理设备

将设备注册或加入到 Azure AD 后，请将 [Azure 门户](https://portal.azure.com/) 用作管理设备标识的中心位置。 通过 "Azure Active Directory 设备" 页，您可以：

* [配置设备设置](device-management-azure-portal.md#configure-device-settings)
* 你需要成为本地管理员才能管理 Windows 设备。 [Azure AD 更新 Azure AD 加入的设备的此成员身份](assign-local-admin.md)，则会自动将设备管理器角色添加为所有已加入设备的管理员。

请确保通过 [管理过时的设备](manage-stale-devices.md)使环境保持整洁，并将资源集中在管理当前设备上。

* [查看与设备相关的审核日志](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>支持的设备管理工具

管理员可以使用其他设备管理工具来保护和进一步控制这些已注册和已加入的设备。 通过这些工具，可强制实施组织要求的配置，例如要求加密存储、密码复杂度、软件安装和软件更新。 

查看集成设备支持和不支持的平台：

| 设备管理工具| 已注册 Azure AD| Azure AD 加入| 混合 Azure AD 加入|
| - | - | - | - |
| [ (MDM) 的移动设备管理 ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>示例： Microsoft Intune| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)|  |
| [与 Microsoft Intune 和 Microsoft 端点 Configuration Manager 的共同管理](/mem/configmgr/comanage/overview) <br> (Windows 10 和更高版本) | | ![这些值的选中标记。](./media/plan-device-deployment/check.png)| ![这些值的选中标记。](./media/plan-device-deployment/check.png)|  |
| [组策略](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>仅 (Windows) | | | ![这些值的选中标记。](./media/plan-device-deployment/check.png)|  |



 建议你考虑在已注册的 iOS 或 Android 设备上 [Microsoft Intune 移动应用程序管理 (MAM) ](/mem/intune/apps/app-management) 以及设备管理。

 管理员还可以将 [虚拟桌面基础结构 (VDI) 平台](howto-device-identity-virtual-desktop-infrastructure.md) （在其组织中托管 Windows 操作系统）来简化管理，并通过整合和集中资源降低成本。 

### <a name="troubleshoot-device-identities"></a>排查设备标识问题

* [使用 dsregcmd.exe 命令对设备进行故障排除](troubleshoot-device-dsregcmd.md)

* [在 Azure Active Directory 中排查企业状态漫游设置问题](enterprise-state-roaming-troubleshooting.md)

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

* [对 Windows 当前设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-current.md)

* [排查 Windows 下层设备的混合 Azure AD 联接的问题](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

* [规划 Azure AD 联接实现](azureadjoin-plan.md)
* [规划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)
* [管理设备标识](device-management-azure-portal.md)