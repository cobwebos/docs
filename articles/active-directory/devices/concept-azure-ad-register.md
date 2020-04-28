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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462754"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 注册设备

Azure AD 注册设备的目标是向用户提供对自带设备（BYOD）或移动设备方案的支持。 在这些情况下，用户可以使用个人设备访问组织的 Azure Active Directory 控制资源。

|   | 已注册 Azure AD |
| --- | --- |
| **定义** | 已注册到 Azure AD，无需组织帐户即可登录到设备 |
| **主要用户** | 适用于具有以下条件的所有用户： |
|   | 自带设备办公 (BYOD) |
|   | 移动设备 |
| **设备所有权** | 用户或组织 |
| **操作系统** | Windows 10、iOS、Android 和 MacOS |
| **设置** | Windows 10 –设置 |
|   | iOS/Android –公司门户或 Microsoft Authenticator 应用 |
|   | MacOS –公司门户 |
| **设备登录选项** | 最终用户本地凭据 |
|   | 密码 |
|   | Windows Hello |
|   | PIN |
|   | 其他设备的生物识别或模式 |
| **设备管理** | 移动设备管理（示例： Microsoft Intune） |
|   | 移动应用程序管理 |
| **关键功能** | SSO 到云资源 |
|   | 注册到 Intune 时的条件性访问 |
|   | 通过应用保护策略的条件性访问 |
|   | 启用 Microsoft Authenticator 应用的手机登录 |

![Azure AD 注册设备](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 注册的设备使用本地帐户（例如 Windows 10 设备上的 Microsoft 帐户）登录，但另外还附加了 Azure AD 帐户来访问组织资源。 根据应用于设备标识的 Azure AD 帐户和条件访问策略，可以进一步限制对组织中资源的访问。

管理员可以使用 Microsoft Intune 等移动设备管理（MDM）工具来保护这些 Azure AD 注册的设备，并进一步控制这些设备。 MDM 提供一种方式来强制实施组织必需的配置，例如需要对存储进行加密、密码复杂性和安全软件更新。 

首次访问工作应用程序或手动使用 Windows 10 设置菜单时，可以完成 Azure AD 注册。 

## <a name="scenarios"></a>方案

你的组织中的用户想要访问用于电子邮件的工具、报告休息时间和从其家庭电脑注册的权益。 你的组织将这些工具用于需要从 Intune 兼容设备访问的条件性访问策略。 用户添加其组织帐户并将其家用 PC 注册到 Azure AD，并强制要求用户访问其资源。

其他用户希望在其已取得 root 权限的个人 Android 手机上访问其组织电子邮件。 你的公司需要相容设备，并创建了 Intune 合规性策略来阻止任何 root 设备设备。 该员工将停止访问此设备上的组织资源。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理陈旧的设备](manage-stale-devices.md)
