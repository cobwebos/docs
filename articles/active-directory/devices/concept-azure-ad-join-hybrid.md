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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85554917"
---
# <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

十多年来，许多组织已使用本地 Active Directory 域加入来实现以下目的：

- IT 部门能够从中央位置管理工作所有设备。
- 用户能够使用其 Active Directory 工作或学校帐户登录他们的设备。

通常，具有本地空间的组织依赖于图像处理方法来预配设备，它们通常使用 **Configuration Manager** 或 **组策略 (GP) ** 进行管理。

如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备是加入本地 Active Directory 并注册到 Azure Active Directory 的设备。

| 混合 Azure AD 联接 | 说明 |
| --- | --- |
| **定义** | 已加入本地 AD 和 Azure AD，要求组织帐户登录到设备 |
| **主要受众** | 适用于具有现有本地 AD 基础结构的混合组织 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | Windows 10、8.1 和7 |
|   | Windows Server 2008/R2、2012/R2、2016和2019 |
| **Provisioning** | Windows 10、Windows Server 2016/2019 |
|   | 域加入并通过 Azure AD Connect 或 ADFS config 进行加入 |
|   | 通过 Windows Autopilot 加入域并通过 Azure AD Connect 或 ADFS config 进行加入 |
|   | Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2-需要 MSI |
| **设备登录选项** | 组织帐户使用： |
|   | 密码 |
|   | 适用于 Win10 的 Windows Hello 企业版 |
| **设备管理** | 组策略 |
|   | Configuration Manager 独立或共同管理 Microsoft Intune |
| **关键功能** | SSO 连接到云和本地资源 |
|   | 通过域加入或通过 Intune 进行的条件性访问（如果共同托管） |
|   | 在锁屏界面上进行自助式密码重置和 Windows Hello PIN 重置 |
|   | 跨设备的企业状态漫游 |

![已加入混合 Azure AD 的设备](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>方案

如果以下情况，请使用 Azure AD 混合加入设备：

- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。
- 要继续使用组策略管理设备配置。
- 要继续使用现有的映像解决方案来部署和配置设备。
- 除 Windows 10 外，还必须支持下级 Windows 7 和8.1 设备

## <a name="next-steps"></a>后续步骤

- [规划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
