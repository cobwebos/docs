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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462754"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 注册设备

Azure AD 注册设备的目标是为用户提供对自带设备 （BYOD） 或移动设备方案的支持。 在这些情况下，用户可以使用个人设备访问组织的 Azure 活动目录控制资源。

|   | Azure AD 已注册 |
| --- | --- |
| **定义** | 注册到 Azure AD，无需组织帐户登录设备 |
| **主要受众** | 适用于所有具有以下条件的用户： |
|   | 自带设备办公 (BYOD) |
|   | 移动设备 |
| **设备所有权** | 用户或组织 |
| **操作系统** | Windows 10、iOS、安卓和 MacOS |
| **设置** | Windows 10 = 设置 |
|   | iOS/安卓 – 公司门户或微软身份验证器应用程序 |
|   | MacOS » 公司门户 |
| **设备登录选项** | 最终用户本地凭据 |
|   | 密码 |
|   | Windows Hello |
|   | PIN |
|   | 其他设备的生物识别或模式 |
| **设备管理** | 移动设备管理（例如：微软Intune） |
|   | 移动应用程序管理 |
| **关键功能** | SSO 到云资源 |
|   | 注册到 Intune 时的条件访问 |
|   | 通过应用保护策略进行条件访问 |
|   | 使用 Microsoft 身份验证器应用启用电话登录 |

![Azure AD 注册设备](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 注册设备已登录到使用本地帐户（如 Windows 10 设备上的 Microsoft 帐户），但另外还附加了 Azure AD 帐户以访问组织资源。 根据应用于设备标识的 Azure AD 帐户和条件访问策略，可以进一步限制对组织中资源的访问。

管理员可以使用 Microsoft Intune 等移动设备管理 （MDM） 工具保护和进一步控制这些 Azure AD 注册设备。 MDM 提供了一种强制实施组织所需的配置的方法，例如要求对存储进行加密、密码复杂性以及安全软件不断更新。 

首次访问工作应用程序或使用 Windows 10 设置菜单手动时，可以完成 Azure AD 注册。 

## <a name="scenarios"></a>方案

组织中的用户希望从其主电脑访问用于电子邮件、报告休假和福利注册的工具。 您的组织在条件访问策略后面拥有这些工具，该策略需要从符合 Intune 的设备进行访问。 用户添加其组织帐户，并将其主 PC 注册到 Azure AD，并强制执行所需的 Intune 策略，以允许用户访问其资源。

另一个用户想要访问他们的组织电子邮件在他们的个人Android手机已经扎根。 您的公司需要符合要求的设备，并创建了 Intune 合规性策略来阻止任何根设备。 员工将停止访问此设备上的组织资源。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理陈旧的设备](manage-stale-devices.md)
