---
title: 什么是混合 Azure AD 联接设备？
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512243"
---
# <a name="hybrid-azure-ad-joined-devices"></a>混合 Azure AD 加入设备

十多年来，许多组织已使用本地 Active Directory 域加入来实现以下目的：

- IT 部门能够从中央位置管理工作所有设备。
- 用户能够使用其 Active Directory 工作或学校帐户登录他们的设备。

通常，具有本地占用空间的组织依赖映像方法来预配设备，并且他们经常使用**配置管理器**或**组策略 （GP）** 来管理设备。

如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备是已加入本地活动目录并在 Azure 活动目录中注册的设备。

|   | 混合 Azure AD 联接 |
| --- | --- |
| **定义** | 已加入本地 AD 和 Azure AD，要求组织帐户登录到设备 |
| **主要受众** | 适用于具有现有本地 AD 基础设施的混合组织 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | 视窗 10、8.1 和 7 |
|   | Windows 服务器 2008/R2， 2012/R2， 2016 和 2019 |
| **设置** | 视窗 10， 视窗服务器 2016/2019 |
|   | IT 加入域并通过 Azure AD 连接或 ADFS 配置自动联接 |
|   | 通过 Windows 自动驾驶仪加入域并通过 Azure AD 连接或 ADFS 配置自动联接 |
|   | Windows 8.1、Windows 7、Windows 服务器 2012 R2、Windows 服务器 2012 和 Windows 服务器 2008 R2 - 需要 MSI |
| **设备登录选项** | 使用： |
|   | 密码 |
|   | 适用于 Win10 的业务窗口你好 |
| **设备管理** | 组策略 |
|   | 配置管理器独立或与微软 Intune 共同管理 |
| **关键功能** | SSO 到云和本地资源 |
|   | 通过域联接或通过 Intune 进行条件访问（如果共同管理） |
|   | 在锁定屏幕上的自助服务密码重置和 Windows Hello PIN 重置 |
|   | 跨设备遍漫游的企业状态 |

![混合 Azure AD 加入设备](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>方案

在：

- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。
- 您希望继续使用组策略来管理设备配置。
- 您希望继续使用现有映像解决方案来部署和配置设备。
- 除了 Windows 10 之外，还必须支持低级 Windows 7 和 8.1 设备

## <a name="next-steps"></a>后续步骤

- [计划混合 Azure AD 加入实现](hybrid-azuread-join-plan.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理陈旧的设备](manage-stale-devices.md)
