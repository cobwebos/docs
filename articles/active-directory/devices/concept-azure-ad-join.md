---
title: Azure AD 联接设备是什么？
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
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512192"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

Azure AD 联接适用于希望成为云优先或仅限云的组织。 无论规模或行业如何，任何组织都可以部署 Azure AD 联接设备。 即使在混合环境中，Azure AD 联接仍有效，同时支持云和本地应用程序和资源的访问权限。

|   | Azure AD Join |
| --- | --- |
| **定义** | 仅加入到需要组织帐户登录到设备 Azure AD |
| **主要用户** | 适用于仅限云的组织和混合组织。 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | 所有 Windows 10 设备 |
| **预配** | 自助服务： Windows OOBE 或设置 |
|   | 批量注册 |
|   | Windows Autopilot |
| **设备登录选项** | 组织帐户使用： |
|   | 密码 |
|   | Windows Hello for Business |
|   | FIDO 2.0 安全密钥（预览版） |
| **设备管理** | 移动设备管理（示例： Microsoft Intune） |
|   | 与 Microsoft Intune 和 Microsoft 终结点的共同管理 Configuration Manager |
| **关键功能** | SSO 到云和本地资源 |
|   | 通过 MDM 注册和 MDM 符合性评估的条件性访问 |
|   | 在锁屏界面上进行自助密码重置和 Windows Hello PIN 重置 |
|   | 跨设备企业状态漫游 |

Azure AD 联接的设备使用组织 Azure AD 帐户登录。 根据应用于设备标识的 Azure AD 帐户和[条件访问策略](../conditional-access/overview.md)，可以进一步限制对组织中资源的访问。

管理员可以使用移动设备管理（MDM）工具（如 Microsoft Intune 或使用 Microsoft 终结点 Configuration Manager 的共同管理方案）来保护和进一步控制 Azure AD 加入的设备。 这些工具提供了一种方法，可强制实施组织必需的配置，例如需要存储加密、密码复杂性、软件安装和软件更新。 管理员可以使用 Configuration Manager 来[管理业务和教育 Microsoft Store 中的应用](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)，使组织应用程序可用于 Azure AD 加入的设备。

可以使用自助服务选项（如全新体验（OOBE）、批量注册或[Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)）来实现 Azure AD join。

Azure AD 联接的设备在组织的网络上时仍可保持对本地资源的单一登录访问。 Azure AD 联接的设备仍可对本地服务器（如文件、打印和其他应用程序）进行身份验证。

## <a name="scenarios"></a>方案

虽然 Azure AD 加入主要面向没有本地 Windows Server Active Directory 基础结构的组织，但当然也可以用于以下方案：

- 要使用 Azure AD 和 MDM（如 Intune）转换到基于云的基础结构。
- 例如，在需要控制平板电脑和手机等设备时，无法使用本地域加入。
- 用户主要需要访问 Office 365 或其他与 Azure AD 集成的 SaaS 应用。
- 你需要在 Azure AD 而不是 Active Directory 中管理用户组。 例如，此方案可以应用于季节性工作人员、承包商或学生。
- 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。

可对 Windows 10 设备配置 Azure AD 加入设备。

Azure AD 加入设备旨在简化：

- 工作所有设备的 Windows 部署
- 从任何 Windows 设备访问组织的应用和资源
- 基于云管理工作所有的设备
- 用户使用其 Azure AD 或 Active Directory 工作或学校帐户登录到其设备。

![Azure AD 加入设备](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD 加入可以通过下列任何方法进行部署：

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [批量部署](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [自助部署体验](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>后续步骤

- [规划 Azure AD 联接实现](azureadjoin-plan.md)
- [如何在 Azure AD 联接设备上管理本地管理员组](assign-local-admin.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [管理 Azure AD 中的过时设备](manage-stale-devices.md)
