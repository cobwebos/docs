---
title: 什么是混合 Azure AD 加入设备？
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462741"
---
# <a name="hybrid-azure-ad-joined-devices"></a>混合 Azure AD 加入设备

十多年来，许多组织已使用本地 Active Directory 域加入来实现以下目的：

- IT 部门能够从中央位置管理工作所有设备。
- 用户能够使用其 Active Directory 工作或学校帐户登录他们的设备。

通常，具有本地占用空间的组织依靠映像方法预配设备，并常常使用 System Center Configuration Manager (SCCM)  或组策略 (GP)  管理这些设备。

如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备是已加入到你的本地 Active Directory 和 Azure Active Directory 中注册的设备。

|   | 混合 Azure AD 联接 |
| --- | --- |
| **定义** | 已加入到的本地 AD 和 Azure AD 要求组织帐户登录到设备 |
| **主要受众** | 适合于与现有的混合型组织的本地 AD 基础结构 |
|   | 适用于在组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | Windows 10、windows 8.1 和 7 |
|   | Windows Server 2008/R2，2012年/R2，2016年和 2019年 |
| **预配** | Windows 10 中，Windows Server 2016/2019 |
|   | 通过域加入 IT 和通过 Azure AD Connect 或 ADFS 配置自动联接 |
|   | 通过 Windows Autopilot 和自动联接 Azure AD Connect 或 ADFS 配置通过域加入 |
|   | Windows 8.1、 Windows 7、 Windows Server 2012 R2、 Windows Server 2012 和 Windows Server 2008 R2-需要 MSI |
| **设备登录选项** | 使用组织帐户： |
|   | 密码 |
|   | Windows hello 企业版的 Win10 |
| **设备管理** | 组策略 |
|   | System Center Configuration Manager 独立或使用 Microsoft Intune 进行共同管理 |
| **主要功能** | 在本地和云资源的 SSO |
|   | 条件性访问通过加入域或 Intune 如果共同管理 |
|   | 自助服务密码重置和 Windows Hello PIN 重置锁定屏幕上 |
|   | 跨设备的企业状态漫游 |

![混合 Azure AD 加入设备](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>方案

使用 Azure AD 混合加入设备，如果：

- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。
- 你想要继续使用组策略管理设备配置。
- 你想要继续使用现有映像解决方案来部署和配置设备。
- 必须支持下级 Windows 7 和 8.1 设备以及 Windows 10

## <a name="next-steps"></a>后续步骤

- [计划混合 Azure AD 加入实现](hybrid-azuread-join-plan.md)
- [管理使用 Azure 门户的设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理过时设备](manage-stale-devices.md)
