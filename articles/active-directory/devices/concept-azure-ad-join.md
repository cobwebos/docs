---
title: 什么是 Azure AD 加入设备？
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462806"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

Azure AD 联接适用于想要为云优先或仅限云的组织。 任何组织可以部署的大小或行业无论 Azure AD 加入设备。 Azure AD 联接适用甚至在混合环境中，启用对在本地和云应用和资源的访问。

|   | Azure AD Join |
| --- | --- |
| **定义** | 仅加入 Azure AD 要求组织帐户登录到设备 |
| **主要受众** | 适用于两者仅限云的和混合型组织。 |
|   | 适用于在组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | 所有 Windows 10 设备 |
| **预配** | 自助服务：Windows OOBE 或设置 |
|   | 批量注册 |
|   | Windows Autopilot |
| **设备登录选项** | 使用组织帐户： |
|   | 密码 |
|   | Windows Hello for Business |
|   | FIDO2.0 安全密钥 （预览版） |
| **设备管理** | 移动设备管理 (示例：Microsoft Intune) |
|   | 使用 Microsoft Intune 和 System Center Configuration Manager 中的共同管理 |
| **主要功能** | 在本地和云资源的 SSO |
|   | 通过 MDM 注册和 MDM 符合性评估的条件性访问 |
|   | 自助服务密码重置和 Windows Hello PIN 重置锁定屏幕上 |
|   | 跨设备的企业状态漫游 |

Azure AD 加入设备登录到使用组织 Azure AD 帐户。 组织中的资源的访问权限可以进一步限制基于该 Azure AD 帐户和[条件性访问策略](../conditional-access/overview.md)应用到的设备标识。

管理员可以保护和进一步控制 Azure AD 加入设备使用移动设备管理 (MDM) 工具如 Microsoft Intune 或使用 System Center Configuration Manager 的共同管理方案中。 这些工具提供一种强制组织所需的配置，如要求存储进行加密、 密码复杂性、 软件安装和软件更新。 管理员可以使组织应用程序供使用的 Azure AD 加入设备[System Center Configuration Manager 和 Microsoft Store for Business](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business)。

可以使用自助服务选项如外出时的全新体验 (OOBE)，批量注册，请完成 azure AD 联接或[Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)。

Azure AD 加入设备仍可以保持对本地资源的单一登录访问组织的网络上时才。 加入了 Azure AD 的设备仍可以对在本地服务器，如文件、 打印和其他应用程序。

## <a name="scenarios"></a>方案

虽然 Azure AD 加入主要面向没有本地 Windows Server Active Directory 基础结构的组织，但当然也可以用于以下方案：

- 要使用 Azure AD 和 MDM（如 Intune）转换到基于云的基础结构。
- 例如，在需要控制平板电脑和手机等设备时，无法使用本地域加入。
- 用户主要需要访问 Office 365 或其他与 Azure AD 集成的 SaaS 应用。
- 你需要在 Azure AD 而不是 Active Directory 中管理用户组。 这种方案可以适用，例如，为季节性工人、 承包商或学生。
- 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。

可对 Windows 10 设备配置 Azure AD 加入设备。

Azure AD 加入设备旨在简化：

- 工作所有设备的 Windows 部署
- 从任何 Windows 设备访问组织的应用和资源
- 基于云管理工作所有的设备
- 用户登录到他们使用其 Azure AD 的设备或已同步的 Active Directory 工作或学校帐户。

![Azure AD 加入设备](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD 加入可以通过下列任何方法进行部署：

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [批量部署](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [自助部署体验](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>后续步骤

- [计划你的 Azure AD 联接实现](azureadjoin-plan.md)
- [如何管理 Azure AD 上的本地管理员组已加入设备](assign-local-admin.md)
- [管理使用 Azure 门户的设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理过时设备](manage-stale-devices.md)
