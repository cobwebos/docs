---
title: 什么是已加入 Azure AD 的设备？
description: 了解 Azure AD 联接的设备，以及设备标识管理如何帮助您管理在您的环境中访问资源的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601446"
---
# <a name="azure-ad-joined-devices"></a>已加入 Azure AD 的设备

“Azure AD 加入”面向的是想要云优先或仅使用云的组织。 无论规模或行业，任何组织都可部署已加入 Azure AD 的设备。 即使在混合环境中，也能使用“Azure AD 加入”，从而提供对云和本地应用及资源的访问。

| Azure AD 加入 | 说明 |
| --- | --- |
| **定义** | 只需用组织帐户登录到设备即可加入 Azure AD |
| **主要受众** | 适合仅使用云和使用混合环境的组织。 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | Windows 10 家庭版以外的所有 Windows 10 设备 |
|   | [在 Azure 中运行的 Windows Server 2019 虚拟机](howto-vm-sign-in-azure-ad-windows.md)（不支持服务器核心） |
| **预配** | 自助服务：Windows OOBE 或设置 |
|   | 批量注册 |
|   | Windows Autopilot |
| **设备登录选项** | 组织帐户使用： |
|   | 密码 |
|   | Windows Hello for Business |
|   | FIDO2.0 安全密钥（预览版） |
| **设备管理** | 移动设备管理（例如：Microsoft Intune） |
|   | 使用 Microsoft Intune 和 Microsoft Endpoint Configuration Manager 共同管理 |
| **关键功能** | SSO 连接到云和本地资源 |
|   | 通过 MDM 注册和 MDM 符合性评估进行条件访问 |
|   | 在锁屏界面上进行自助式密码重置和 Windows Hello PIN 重置 |
|   | 跨设备的企业状态漫游 |

使用 Azure AD 组织帐户登录已加入 Azure AD 的设备。 可根据 Azure AD 帐户和设备标识所应用的[条件访问策略](../conditional-access/howto-conditional-access-policy-compliant-device.md)，进一步限制对组织中的资源的访问。

管理员可使用 Microsoft Intune 等移动设备管理 (MDM) 工具或使用 Microsoft Endpoint Configuration Manager（在共同管理场景中）来保护和进一步控制已加入 Azure AD 的设备。 通过这些工具，可强制实施组织要求的配置，例如要求加密存储、密码复杂度、软件安装和软件更新。 管理员可使用 Configuration Manager [管理来自适用于企业和教育的 Microsoft Store 的应用](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)，将组织应用程序提供给已加入 Azure AD 的设备。

可使用开箱即用体验 (OOBE)、批量注册或 [Windows Autopilot](/intune/enrollment-autopilot) 等自助服务选项来实现加入 Azure AD 的操作。

已加入 Azure AD 的设备在位于组织的网络上时，仍可保证对本地资源进行单一登录访问。 这些设备仍可向文件、打印和其他应用程序等本地服务器进行身份验证。

## <a name="scenarios"></a>方案

虽然 Azure AD 加入主要面向没有本地 Windows Server Active Directory 基础结构的组织，但当然也可以用于以下方案：

- 要使用 Azure AD 和 MDM（如 Intune）转换到基于云的基础结构。
- 例如，在需要控制平板电脑和手机等设备时，无法使用本地域加入。
- 用户主要需要访问 Microsoft 365 或其他与 Azure AD 集成的 SaaS 应用。
- 你需要在 Azure AD 而不是 Active Directory 中管理用户组。 该方案适用于季节工、承包商或学生等群体。
- 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。

你可以为所有 Windows 10 设备配置 Azure AD 联接设备，Windows 10 家庭版除外。

Azure AD 加入设备旨在简化：

- 工作所有设备的 Windows 部署
- 从任何 Windows 设备访问组织的应用和资源
- 基于云管理工作所有的设备
- 用户可使用其 Azure AD 或已同步的 Active Directory 工作或学校帐户登录其设备。

![Azure AD 加入设备](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD 加入可以通过下列任何方法进行部署：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [批量部署](/intune/windows-bulk-enroll)
- [自助部署体验](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>后续步骤

- [计划 Azure AD 加入实现](azureadjoin-plan.md)
- [如何管理已加入 Azure AD 的设备上的本地管理员组](assign-local-admin.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
