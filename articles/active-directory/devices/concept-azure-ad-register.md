---
title: 什么是 Azure AD 注册设备？
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462754"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 注册设备

Azure AD 注册设备的目标是为用户提供并支持自带设备办公 (BYOD) 或移动设备方案。 在这些情况下，用户可以访问你组织的 Azure Active Directory 控制资源使用的是个人设备。

|   | 已注册 azure AD |
| --- | --- |
| **定义** | 而无需组织帐户登录到设备注册到 Azure AD |
| **主要受众** | 适用于具有以下条件的所有用户： |
|   | 自带设备办公 (BYOD) |
|   | 移动设备 |
| **设备所有权** | 用户或组织 |
| **操作系统** | Windows 10、 iOS、 Android 和 MacOS |
| **预配** | Windows 10-设置 |
|   | iOS/Android – 公司门户或 Microsoft Authenticator 应用 |
|   | MacOS-公司门户 |
| **设备登录选项** | 最终用户的本地凭据 |
|   | 密码 |
|   | Windows Hello |
|   | PIN |
|   | 生物识别或其他设备模式 |
| **设备管理** | 移动设备管理 (示例：Microsoft Intune) |
|   | 移动应用程序管理 |
| **主要功能** | 对云资源的 SSO |
|   | 当注册到 Intune 时的条件性访问 |
|   | 通过应用保护策略的条件性访问 |
|   | 启用使用 Microsoft Authenticator 应用手机登录 |

![Azure AD 注册设备](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 注册设备的 Windows 10 设备上，使用 Microsoft 帐户之类的本地帐户登录，但另外有访问附加到组织的资源的 Azure AD 帐户。 基于该 Azure AD 帐户的更多受限，条件性访问策略应用到的设备标识，可以是组织中的资源的访问权限。

管理员可以保护和进一步控制这些已注册的 Azure AD 的设备使用 Microsoft Intune 等移动设备管理 (MDM) 工具。 MDM 提供了一种强制组织所需的配置，如要求存储进行加密、 密码复杂性和保留已更新的安全软件。 

访问工作应用程序第一次或手动使用 Windows 10 设置菜单时，可以完成 azure AD 注册。 

## <a name="scenarios"></a>方案

你的组织中的用户想要访问电子邮件，工具报告时间关闭和从其家用电脑权益注册。 你的组织有需要从 Intune 兼容设备的访问权限的条件性访问策略背后的这些工具。 用户将添加其组织帐户，并向 Azure AD 注册其家庭 PC 和所需的 Intune 策略强制实施向其资源的用户权限。

另一个用户想要访问其已获得 root 权限的个人 Android 手机上其组织的电子邮件。 你的公司需要兼容设备和已创建的 Intune 符合性策略来阻止取得 root 权限的任何设备。 员工访问组织资源，此设备上的已停止。

## <a name="next-steps"></a>后续步骤

- [管理使用 Azure 门户的设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理过时设备](manage-stale-devices.md)
