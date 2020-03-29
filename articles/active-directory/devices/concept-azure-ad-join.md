---
title: 什么是 Azure AD 联接设备？
description: 了解设备标识管理如何帮助你管理正在访问环境中的资源的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672677"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

Azure AD 联接适用于希望成为云优先或仅云的组织。 任何组织都可以部署 Azure AD 联接的设备，无论大小或行业如何。 Azure AD 联接即使在混合环境中也能正常工作，从而允许访问云和本地应用和资源。

|   | Azure AD Join |
| --- | --- |
| **定义** | 仅加入 Azure AD，要求组织帐户登录到设备 |
| **主要受众** | 适用于仅云和混合组织。 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | 所有 Windows 10 设备 |
| **设置** | 自助服务：Windows OOBE 或设置 |
|   | 批量注册 |
|   | Windows Autopilot |
| **设备登录选项** | 使用： |
|   | 密码 |
|   | Windows Hello for Business |
|   | FIDO2.0 安全密钥（预览版） |
| **设备管理** | 移动设备管理（例如：微软Intune） |
|   | 与微软 Intune 和微软端点配置管理器共同管理 |
| **关键功能** | SSO 到云和本地资源 |
|   | 通过 MDM 注册和 MDM 合规性评估进行条件访问 |
|   | 在锁定屏幕上的自助服务密码重置和 Windows Hello PIN 重置 |
|   | 跨设备遍漫游的企业状态 |

Azure AD 联接的设备已登录到使用组织 Azure AD 帐户。 根据应用于设备标识的 Azure AD 帐户和[条件访问策略](../conditional-access/overview.md)，可以进一步限制对组织中资源的访问。

管理员可以使用移动设备管理 （MDM） 工具（如 Microsoft Intune）或使用 Microsoft 端点配置管理器在共同管理方案中保护和进一步控制 Azure AD 联接的设备。 这些工具提供了一种实施组织所需的配置的方法，例如要求对存储进行加密、密码复杂性、软件安装和软件更新。 管理员可以使用配置管理器将组织应用程序提供给 Azure AD 联接的设备，以[管理来自 Microsoft 应用商店的商业和教育应用](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)。

Azure AD 联接可以使用自助服务选项（如开箱即用体验 （OOBE）、批量注册或[Windows 自动驾驶仪等完成](/intune/enrollment-autopilot)。

Azure AD 加入的设备在组织网络上时仍可以维护对本地资源的单一登录访问权限。 Azure AD 联接的设备仍然可以对本地服务器（如文件、打印和其他应用程序）进行身份验证。

## <a name="scenarios"></a>方案

虽然 Azure AD 加入主要面向没有本地 Windows Server Active Directory 基础结构的组织，但当然也可以用于以下方案：

- 要使用 Azure AD 和 MDM（如 Intune）转换到基于云的基础结构。
- 例如，在需要控制平板电脑和手机等设备时，无法使用本地域加入。
- 用户主要需要访问 Office 365 或其他与 Azure AD 集成的 SaaS 应用。
- 你需要在 Azure AD 而不是 Active Directory 中管理用户组。 例如，此方案可以适用于季节性工人、承包商或学生。
- 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。

可对 Windows 10 设备配置 Azure AD 加入设备。

Azure AD 加入设备旨在简化：

- 工作所有设备的 Windows 部署
- 从任何 Windows 设备访问组织的应用和资源
- 基于云管理工作所有的设备
- 用户使用 Azure AD 或同步的活动目录工作或学校帐户登录到其设备。

![Azure AD 加入设备](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD 加入可以通过下列任何方法进行部署：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [批量部署](/intune/windows-bulk-enroll)
- [自助部署体验](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>后续步骤

- [计划 Azure AD 加入实现](azureadjoin-plan.md)
- [如何管理已加入 Azure AD 的设备上的本地管理员组](assign-local-admin.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理陈旧的设备](manage-stale-devices.md)
