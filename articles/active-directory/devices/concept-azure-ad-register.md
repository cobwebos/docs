---
title: 什么是 Azure AD 注册设备？
description: 了解 Azure AD 注册的设备如何向用户提供对自带设备 (BYOD) 或移动设备方案的支持。
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
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91256432"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 注册设备

Azure AD 注册设备的目标是向用户提供对自带设备的支持 (BYOD) 或移动设备方案。 在这些情况下，用户可以使用个人设备访问组织的 Azure Active Directory 控制资源。

| 已注册 Azure AD | 说明 |
| --- | --- |
| **定义** | 已注册到 Azure AD，无需组织帐户即可登录到设备 |
| **主要受众** | 适用于具有以下条件的所有用户： |
|   | 自带设备办公 (BYOD) |
|   | 移动设备 |
| **设备所有权** | 用户或组织 |
| **操作系统** | Windows 10、iOS、Android 和 MacOS |
| **Provisioning** | Windows 10 –设置 |
|   | iOS/Android –公司门户或 Microsoft Authenticator 应用 |
|   | MacOS –公司门户 |
| **设备登录选项** | 最终用户本地凭据 |
|   | Password |
|   | Windows Hello |
|   | PIN |
|   | 其他设备的生物识别或模式 |
| **设备管理** | 移动设备管理（例如：Microsoft Intune） |
|   | 移动应用程序管理 |
| **关键功能** | SSO 到云资源 |
|   | 注册到 Intune 时的条件性访问 |
|   | 通过应用保护策略的条件性访问 |
|   | 启用 Microsoft Authenticator 应用的手机登录 |

![Azure AD 注册设备](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 注册的设备使用本地帐户（例如 Windows 10 设备上的 Microsoft 帐户）登录，但另外还附加了 Azure AD 帐户来访问组织资源。 可根据 Azure AD 帐户和设备标识所应用的条件访问策略，进一步限制对组织中的资源的访问。

管理员可以使用移动设备管理来保护这些 Azure AD 注册的设备，并 (MDM) 工具，如 Microsoft Intune。 MDM 提供一种方式来强制实施组织必需的配置，例如需要对存储进行加密、密码复杂性和安全软件更新。 

首次访问工作应用程序或手动使用 Windows 10 设置菜单时，可以完成 Azure AD 注册。 

## <a name="scenarios"></a>方案

你的组织中的用户想要访问用于电子邮件的工具、报告休息时间和从其家庭电脑注册的权益。 你的组织将这些工具用于需要从 Intune 兼容设备访问的条件性访问策略。 用户添加其组织帐户并将其家用 PC 注册到 Azure AD，并强制要求用户访问其资源。

其他用户希望在其已取得 root 权限的个人 Android 手机上访问其组织电子邮件。 你的公司需要相容设备，并创建了 Intune 合规性策略来阻止任何 root 设备设备。 该员工将停止访问此设备上的组织资源。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
