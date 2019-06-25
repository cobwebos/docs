---
title: 如何为-需要托管使用 Azure Active Directory 条件性访问的云应用访问权限的设备 |Microsoft Docs
description: 了解如何配置 Azure Active Directory (Azure AD) 基于设备的条件性访问策略要求的云应用的访问权限的被管理的设备。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 459dd981b73ae840b3fc61bd0cc83ecefb1cf393
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112148"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>如何：需要使用条件性访问的云应用访问权限的被管理的设备

在移动优先、云优先的世界中，使用 Azure Active Directory (Azure AD) 可以实现从任意位置以单一登录方式登录到应用和服务。 经授权的用户可以从一系列设备（包括移动设备和个人设备）访问云应用。 但是，许多环境中至少有几个应用会要求仅通过满足你的安全性和符合性标准的设备进行访问。 这些设备也称为受管理设备。 

本文介绍如何配置需要使用托管的设备来访问您的环境中某些云应用的条件性访问策略。 


## <a name="prerequisites"></a>必备组件

需要为云应用程序访问将被管理的设备**Azure AD 条件访问**并**Azure AD 设备管理**在一起。 如果尚不熟悉其中的某项功能，应该先阅读以下主题:

- **[Azure Active Directory 中的条件性访问](../active-directory-conditional-access-azure-portal.md)** -本文提供的条件性访问和相关的术语的概念性概述。

- **[Azure Active Directory 中的设备管理简介](../devices/overview.md)** - 此文章概述了可以用来将设备置于组织控制下的各种选项。 


## <a name="scenario-description"></a>方案描述

掌控安全性与工作效率之间的平衡是一个难题。 扩大用来访问云资源的受支持设备的范围有助于提高用户的工作效率。 另一方面，你可能不希望具有未知保护级别的设备访问你的环境中的某些资源。 对于受影响的资源，你应当要求用户只能使用受管理设备访问它们。 

使用 Azure AD 条件性访问可以解决这一要求使用一个策略，授予访问权限：

- 授予对所选云应用的访问权限

- 为所选用户和组授予权限

- 要求使用受管理设备


## <a name="managed-devices"></a>受管理设备  

简而言之，受管理设备是指处于*某种*组织控制之下的设备。 在 Azure AD 中，受管理设备的先决条件是它已向 Azure AD 注册。 注册设备时会以设备对象的形式为设备创建标识。 Azure 使用此对象来跟踪设备的状态信息。 作为 Azure AD 管理员，你可以使用此对象切换（启用/禁用）设备状态。
  
![基于设备的条件](./media/require-managed-devices/32.png)

若要向 Azure AD 注册设备，你有三种选择：

- **[Azure AD 注册设备](../devices/overview.md#azure-ad-registered-devices)** ：向 Azure AD 注册个人设备

- **[加入 Azure AD 的设备](../devices/overview.md#azure-ad-joined-devices)** - 向 Azure AD 注册未加入本地 AD 的组织 Windows 10 设备。 

- **[加入混合 Azure AD 的设备](../devices/overview.md#hybrid-azure-ad-joined-devices)** - 向 Azure AD 注册已加入本地 AD 的 Windows 10 或受支持的低级别设备。

若要成为受管理设备，注册设备必须是**加入混合 Azure AD 的设备**或者是**已标记为合规的设备**。  

![基于设备的条件](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>要求使用加入混合 Azure AD 的设备

在条件性访问策略中，可以选择**需要混合 Azure AD 加入的设备**来声明，可仅使用托管的设备访问所选的云应用。 

![基于设备的条件](./media/require-managed-devices/10.png)

此设置仅适用于已加入本地 AD 的 Windows 10 或低级别设备（例如 Windows 7 或 Windows 8）。 你只能使用混合 Azure AD 加入功能向 Azure AD 注册这些设备，这是一种注册 Windows 10 设备的[自动化过程](../devices/hybrid-azuread-join-plan.md)。 

![基于设备的条件](./media/require-managed-devices/45.png)

怎样使加入混合 Azure AD 的设备成为受管理设备？  对于加入本地 AD 的设备，假定使用管理解决方案（如 **System Center Configuration Manager (SCCM)** ）或**组策略 (GP)** 对这些设备进行控制来管理它们。 由于 Azure AD 无法确定是否已向设备应用这些方法中的任何一种，因此，在要求使用受管理设备的情况下，要求使用加入混合 Azure AD 的设备是一种相对较弱的机制。 如果加入本地域的设备同时也是加入混合 Azure AD 的设备，则由管理员判断应用于此类设备的方法是否强大到足以使其成为受管理设备。


## <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

“要求将设备标记为合规”选项是一种用于请求受管理设备的最强大的形式  。

![基于设备的条件](./media/require-managed-devices/11.png)

此选项要求向 Azure AD 注册设备，此外还要求通过以下方式将该设备标记为合规：
         
- Intune。
- 第三方移动设备管理 (MDM) 系统，该系统通过 Azure AD 集成管理 Windows 10 设备。 不支持除 Windows 10 以外的设备 OS 类型的第三方 MDM 系统。
 
![基于设备的条件](./media/require-managed-devices/46.png)



对于标记为合规的设备，你可以假设： 

- 员工用来访问公司数据的移动设备是受管理设备
- 员工使用的移动应用是受管理应用
- 通过帮助控制员工访问和共享公司信息的方式，为公司信息提供保护
- 该设备及其应用符合公司安全要求




## <a name="next-steps"></a>后续步骤

在您的环境中配置基于设备的条件访问策略之前, 应看一看[的 Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。

