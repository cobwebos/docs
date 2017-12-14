---
title: "配置 Azure Active Directory 基于设备的条件访问策略 | Microsoft Docs"
description: "了解如何配置 Azure Active Directory 基于设备的条件访问策略。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: fb646a56b51960d5b076027d942dabe8f2afbe97
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>配置 Azure Active Directory 基于设备的条件访问策略

使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以精细控制经授权的用户如何访问资源。 例如，可以限制为只允许受信任的设备访问特定的资源。 要求设备受信任的条件访问策略也称为基于设备的条件访问策略。

本主题提供有关如何针对连接到 Azure AD 的应用程序配置基于设备的条件访问策略的信息。 


## <a name="before-you-begin"></a>开始之前

基于设备的条件访问将 **Azure AD 条件访问**与 **Azure AD 设备管理**结合在一起。 如果尚不熟悉其中的某项功能，应该先阅读以下主题:

- **[Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)** - 此主题提供条件访问和相关术语的概念性概述。

- **[Azure Active Directory 中的设备管理简介](device-management-introduction.md)** - 此主题概述将设备与 Azure AD 相连接时需要使用的各种选项。 


## <a name="trusted-devices"></a>受信任的设备

在移动优先、云优先的世界中，使用 Azure Active Directory 可以实现从任意位置单一登录到设备、应用和服务。 对于环境中的某些资源，将访问权限授予适当的用户不可能并不足够。 除了向适当的用户授权以外，可能还需要使用受信任的设备来访问资源。 在环境中，可以基于以下组件定义什么是受信任的设备：

- 设备上的[设备平台](active-directory-conditional-access-azure-portal.md#device-platforms)
- 设备是否合规
- 设备是否已加入域 

[设备平台](active-directory-conditional-access-azure-portal.md#device-platforms)根据设备上运行的操作系统来定义特征。 在基于设备的条件访问策略中，可将特定资源的访问权限限制给特定的设备平台。



在基于设备的条件访问策略中，可以要求将受信任的设备标记为合规。

![云应用](./media/active-directory-conditional-access-policy-connected-applications/24.png)

可通过以下途径在目录中将设备标记为合规：

- Intune 
- 第三方移动设备托管系统，该系统通过 Azure AD 集成管理 Windows 10 设备 
 
  

只能将连接到 Azure AD 的设备标记为合规。 若要将设备连接到 Azure Active Directory，可使用以下选项： 

- 已注册 Azure AD
- 已加入 Azure AD
- 已加入混合 Azure AD

    ![云应用](./media/active-directory-conditional-access-policy-connected-applications/26.png)

如果部署了本地 Active Directory (AD)，可以考虑将未连接到 Azure AD、但已加入本地 AD 的设备标记为受信任设备。

![云应用](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>后续步骤

在环境中配置基于设备的条件访问策略之前，应该参阅 [Azure Active Directory 中的条件访问最佳做法](active-directory-conditional-access-best-practices.md)。

