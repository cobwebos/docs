---
title: Azure Active Directory 中的设备管理简介 | Microsoft Docs
description: 了解设备管理如何有助于控制访问环境中的资源的设备。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5d5ddd83a2335e7a08bac587aa627f1dcae9738c
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970894"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Azure Active Directory 中的设备管理简介

在移动优先、云优先的世界中，使用 Azure Active Directory (Azure AD) 可以实现从任意位置单一登录到设备、应用和服务。 随着自带设备 (BYOD) 等设备的普及，IT 专业人员面临着两个对立的目标：

- 使最终用户能够随时随地保持高效的工作
- 随时保持企业资产

用户可通过设备访问企业资产。 为了保护企业资产，IT 管理员需要控制这些设备。 这可确保用户使用满足安全性和符合性标准的设备访问资源。 

设备管理也是[基于设备的条件性访问](active-directory-conditional-access-policy-connected-applications.md)的基础。 通过基于设备的条件访问，可确保只有受管理设备才能访问环境中的资源。   

本文介绍了 Azure Active Directory 中设备管理的工作原理。

## <a name="getting-devices-under-the-control-of-azure-ad"></a>使设备受到 Azure AD 控制

通过以下两种方式可使设备受到 Azure AD 控制：

- 注册 
- 加入

通过向 Azure AD 注册设备可管理设备标识。 在注册设备时，Azure AD 设备注册会为设备提供一个标识，此标识用于在用户登录 Azure AD 时对设备进行身份验证。 可使用此标识启用或禁用设备。

当与 Microsoft Intune 之类的移动设备管理 (MDM) 解决方案结合使用时，Azure AD 中的设备属性将使用关于设备的更多信息进行更新。 这会允许创建条件性访问规则，用于从设备强制访问满足安全和合规性标准。 有关在 Microsoft Intune 中注册设备的详细信息，请参阅“在 Intune 中注册要管理的设备”。

加入设备是注册设备的扩展。 这意味着，加入设备除可提供注册设备的所有优势之外，还可更改设备的本地状态。 更改本地状态后，用户可以使用组织的工作或学校帐户而不是个人帐户登录设备。

## <a name="azure-ad-registered-devices"></a>Azure AD 注册设备   

Azure AD 注册设备的目的是提供自带设备 (BYOD) 方案支持。 此方案中，用户可使用个人设备访问受 Azure Active Directory 控制的组织资源。  

![Azure AD 注册设备](./media/device-management-introduction/03.png)

此访问基于设备上已输入的工作或学校帐户。  
例如，在 Windows 10 中，用户可以将工作或学校帐户添加到个人计算机、平板电脑或手机。  
用户已添加工作或学校帐户后，该设备会注册到 Azure AD，并且可选择在组织已配置的移动设备管理 (MDM) 系统中进行注册。 组织中的用户可以方便地将工作或学校帐户添加到个人设备：

- 首次访问工作应用程序时
- 使用 Windows 10 的情况下，通过“设置”菜单手动进行 

可配置适用于 Windows 10、iOS、Android 和 macOS 的 Azure AD 注册设备。

## <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

Azure AD 加入设备旨在简化：

- 工作所有设备的 Windows 部署 
- 从任何 Windows 设备访问组织的应用和资源
- 基于云管理工作所有的设备

![Azure AD 注册设备](./media/device-management-introduction/02.png)

Azure AD 加入可以通过下列任何方法进行部署： 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [批量部署](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [自助部署体验](device-management-azuread-joined-devices-frx.md) 

Azure AD 加入适用于希望成为云优先（即主要使用云服务，旨在减少使用本地基础结构）或仅限云（无本地基础结构）的组织。 对可以部署 Azure AD 加入的组织的规模或类型没有任何限制。 即使在混合环境中，Azure AD 加入也可以良好运行，提供对云和本地应用及资源的访问。

实现 Azure AD 加入设备具有以下优势：

- 单一登录 (SSO) 到 Azure 托管的 SaaS 应用和服务。 访问工作资源时，不会提示用户进行其他身份验证。 即使在用户未连接到域网络时也可使用 SSO 功能。

- 以符合企业规范的方式在加入设备间漫游用户设置。 用户无需连接 Microsoft 帐户（例如 Hotmail）即可查看设备间的设置。

- 使用 Azure AD 帐户访问适用于企业的 Microsoft Store。 用户可从组织预选的应用程序清单中进行选择。

- 支持 Windows Hello，可安全便捷地访问工作资源。

- 仅限使用满足符合性策略的设备访问应用。

- 当设备与本地域控制器建立直接连接时，**可以无缝访问本地资源**。 


虽然 Azure AD 加入主要面向没有本地 Windows Server Active Directory 基础结构的组织，但当然也可以用于以下方案：

- 要使用 Azure AD 和 MDM（如 Intune）转换到基于云的基础结构。

- 例如，在需要控制平板电脑和手机等设备时，无法使用本地域加入。

- 用户主要需要访问 Office 365 或其他与 Azure AD 集成的 SaaS 应用。

- 你需要在 Azure AD 而不是 Active Directory 中管理用户组。 这适用于季节工人、承包商或学生等群体。

- 想要向本地基础设施有限的远程分支机构中的员工提供加入功能。

可对 Windows 10 设备配置 Azure AD 加入设备。


## <a name="hybrid-azure-ad-joined-devices"></a>混合 Azure AD 加入设备

十多年来，许多组织已使用本地 Active Directory 域加入来实现以下目的：

- IT 部门能够从中央位置管理工作所有设备。

- 用户能够使用其 Active Directory 工作或学校帐户登录他们的设备。 

通常，具有本地占用空间的组织依靠映像方法预配设备，并常常使用 System Center Configuration Manager (SCCM) 或组策略 (GP) 管理这些设备。

如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备同时加入到本地 Active Directory 和 Azure Active Directory。

![Azure AD 注册设备](./media/device-management-introduction/01.png)


以下情况下应选择使用 Azure AD 混合加入设备：

- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。

- 必须使用 GP 才能管理设备。

- 想要继续使用映像解决方案为员工配置设备。

可以对 Windows 10 或低版本系统（例如 Windows 8 和 Windows 7）设备配置混合 Azure AD 加入设备。

## <a name="summary"></a>摘要

使用 Azure AD 中的设备管理能够： 

- 简化将设备交给 Azure AD 控制的流程

- 方便用户访问组织中基于云的资源

凭借经验，应该执行以下操作：

- Azure AD 注册设备：

    - 对于个人设备 

    - 手动将设备注册到 Azure AD

- 已加入 Azure AD 的设备： 

    - 对于组织拥有的设备

    - 对于**未**加入到本地 AD 的设备

    - 手动将设备注册到 Azure AD

    - 更改设备的本地状态

- 对已加入到本地 AD 的设备使用混合 Azure AD 加入设备     

    - 对于组织拥有的设备

    - 对于已加入到本地 AD 的设备

    - 自动将设备注册到 Azure AD

    - 更改设备的本地状态



## <a name="next-steps"></a>后续步骤

- 若要大致了解如何在 Azure 门户中管理设备，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)

- 有关基于设备的条件性访问的详细信息，请参阅[配置 Azure Active Directory 基于设备的条件性访问策略](active-directory-conditional-access-policy-connected-applications.md)。

- 设备设置：
    - 若要设置已注册 Azure Active Directory 的 Windows 10 设备，请参阅[如何配置已注册 Azure Active Directory 的 Windows 10 设备](device-management-azuread-registered-devices-windows10-setup.md)
    - 若要设置已加入 Azure Active Directory 的设备，请参阅[如何配置已加入 Azure Active Directory 的设备](device-management-azuread-joined-devices-setup.md)
    - 若要设置已加入混合 Azure AD 的设备，请参阅[如何配置已加入混合 Azure Active Directory 的设备](device-management-hybrid-azuread-joined-devices-setup.md)。


