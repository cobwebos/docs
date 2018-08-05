---
title: 使用 Azure 门户管理设备 | Microsoft Docs
description: 了解如何使用 Azure 门户管理设备。
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
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: fd810f6e5a4211f64044a88ae6ca6bb6dc044e28
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416110"
---
# <a name="managing-devices-using-the-azure-portal"></a>使用 Azure 门户管理设备


使用 Azure Active Directory (Azure AD) 中的设备管理，可以确保用户从满足安全性和符合性标准的设备访问资源。 

本文：

- 假设用户熟悉 [Azure Active Directory 中的设备管理简介](overview.md)

- 提供有关如何使用 Azure 门户管理设备的信息

## <a name="manage-devices"></a>管理设备 

Azure 门户提供了用于管理设备的集中场所。 可以使用[直接链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或按照以下手动步骤转到此位置：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧导航栏中，单击“Active Directory”。

    ![配置设备设置](./media/device-management-azure-portal/01.png)

3. 在“管理”部分单击“设备”。

    ![配置设备设置](./media/device-management-azure-portal/11.png)
 
可在“设备”页上完成以下操作：

- 配置设备管理设置

- 查找设备

- 执行设备管理任务

- 查看与设备管理相关的审核日志  
  

## <a name="configure-device-settings"></a>配置设备设置

要使用 Azure 门户管理设备，这些设备需要[已注册或已加入](overview.md#getting-devices-under-the-control-of-azure-ad) Azure AD。 作为管理员，可以通过配置设备设置来微调注册和加入设备的过程。 

![配置设备设置](./media/device-management-azure-portal/22.png)

使用设备设置页，可以配置：

![管理 Intune 设备](./media/device-management-azure-portal/21.png)


- **用户可将设备加入 Azure AD** - 可通过此设置选择可以[将设备加入](overview.md#azure-ad-joined-devices) Azure AD 的用户。 默认值为“全部”。

- **已加入 Azure AD 设备上的其他本地管理员** - 可选择具有此设备的本地管理员权限的用户。 此处添加的用户会添加到 Azure AD 中的“设备管理员”角色。 默认情况下，Azure AD 中的全局管理员和设备所有者均具有本地管理员权限。 此选项属于高级版功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。 

- **用户可向 Azure AD 注册其设备** - 需要配置此设置，以允许向 Azure AD [注册](overview.md#azure-ad-registered-devices)设备。 如果选择“无”，则在设备未加入 Azure AD 或混合 Azure AD 时，不允许进行注册。 登记到 Microsoft Intune 或 Office 365 移动设备管理 (MDM) 需要进行注册。 如果已配置其中的任一服务，则会选中“全部”且“无”不可用。

- **需要进行多重身份验证才能加入设备** - 可以选择用户是否需要进行第二重身份验证因素才能将其设备[加入](overview.md#azure-ad-joined-devices) Azure AD。 默认值为“否”。 在注册设备时，建议要求多重身份验证。 为此设备启用多重身份验证前，必须确保已针对注册其设备的用户配置多重身份验证。 有关各种 Azure 多重身份验证服务的详细信息，请参阅 [Azure 多重身份验证入门](../authentication/concept-mfa-whichversion.md)。 

- **最大设备数** - 可通过此设置选择用户可在 Azure AD 中拥有的最大设备数。 如果用户达到此配额，则必须先删除一个或多个现有设备，然后才可添加其他设备。 设备配额会计入当前已加入 Azure AD 或已注册 Azure AD 的所有设备。 默认值为 20。

- **用户可跨设备同步设置和应用数据** - 此设置默认为“无”。 选择特定用户或组，或者选择“全部”，允许用户跨其 Windows 10 设备同步设置和应用数据。 深入了解 Windows 10 中同步的工作原理。
此选项属于高级功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。
 




## <a name="locate-devices"></a>查找设备

可通过两个选项查找已注册和已加入的设备：

- “设备”页的“管理”部分中的“所有设备”  

    ![所有设备](./media/device-management-azure-portal/41.png)


- “用户”页的“管理”部分中的“设备”
 
    ![所有设备](./media/device-management-azure-portal/43.png)



使用这两个选项均可获得一个具有以下功能的视图：


- 支持使用显示名称作为筛选器搜索设备。

- 提供有关已注册和已加入设备的详细概述

- 支持执行常见的设备管理任务
   

![所有设备](./media/device-management-azure-portal/51.png)

对于某些 iOS 设备，包含单引号的设备名可能会使用看起来像单引号的不同字符。 因此搜索此类设备时会有点困难 - 如果没有看到正确的搜索结果，请确保搜索字符串包含匹配的单引号字符。

## <a name="device-management-tasks"></a>设备管理任务

作为管理员，可以管理已注册或已加入设备。 本部分介绍有关常见设备管理任务的信息。


### <a name="manage-an-intune-device"></a>管理 Intune 设备

Intune 管理员可以管理标记为“Microsoft Intune”的设备。 管理员可以查看其他设备 

![管理 Intune 设备](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>启用/禁用 Azure AD 设备

若要启用/禁用设备，可以使用两个选项：

- “所有设备”页上的任务菜单（“...”）

    ![管理 Intune 设备](./media/device-management-azure-portal/71.png)

- “设备”页上的工具栏

    ![管理 Intune 设备](./media/device-management-azure-portal/32.png)


**备注：**

- 需要具有 Azure AD 中的全局管理员身份才能启用/禁用设备。 
- 禁用设备可阻止设备访问你的 Azure AD 资源。 



### <a name="delete-an-azure-ad-device"></a>删除 Azure AD 设备

若要删除设备，可以使用两个选项：

- “所有设备”页上的任务菜单（“...”）

    ![管理 Intune 设备](./media/device-management-azure-portal/72.png)

- “设备”页上的工具栏

    ![删除设备](./media/device-management-azure-portal/34.png)


**备注：**

- 必须是 Azure AD 中的全局管理员或 Intune 管理员才能删除设备。

- 删除设备：
 
    - 可阻止设备访问你的 Azure AD 资源。 

    - 可删除附加到设备的所有详细信息，例如适用于 Windows 设备的 BitLocker 密钥。  

    - 表示一个不可恢复的活动，除非必需，否则不建议。

如果设备由另一管理机构（例如 Microsoft Intune）管理，请确保在 Azure AD 中删除前，已擦除/停用该设备。

 


### <a name="view-or-copy-device-id"></a>查看或复制设备 ID

可以使用设备 ID 在设备上验证设备 ID 详细信息或在故障排除期间使用 PowerShell。 要访问复制选项，请单击设备。

![查看设备 ID](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>查看或复制 BitLocker 密钥

你可以查看和复制 BitLocker 密钥，帮助用户恢复其加密的驱动器。 这些密钥仅适用于已加密并将其密钥存储在 Azure AD 中的 Windows 设备。 访问设备的详细信息时，可以复制这些密钥。
 
![查看 BitLocker 密钥](./media/device-management-azure-portal/36.png)

若要查看或复制 BitLocker 密钥，你需要是设备所有者，或者是至少分配了以下一个角色的用户：

- 全局管理员
- Helpdesk Admin
- 安全管理员
- 安全读者
- Intune 服务管理员

> [!NOTE]
> 加入混合 Azure AD 的 Windows 10 设备没有所有者。 因此，如果按所有者查找设备未找到它，请按设备 ID 搜索。


## <a name="audit-logs"></a>审核日志


设备活动通过活动日志提供。 这包括由设备注册服务或用户触发的活动：

- 创建设备并在设备上添加所有者/用户

- 更改设备设置

- 删除设备或更新设备等设备操作
 
审核数据的入口点为“设备”页的“活动”部分中的“审核日志”。

![审核日志](./media/device-management-azure-portal/61.png)


审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间

- 目标

- 活动的发起者/参与者（人员）

- 活动（内容）

![审核日志](./media/device-management-azure-portal/63.png)

单击工具栏中的“列”即可自定义列表视图。
 
![审核日志](./media/device-management-azure-portal/64.png)


要将所报告数据的范围缩小到适当的级别，可以使用以下字段筛选审核数据：

- 类别
- 活动资源类型
- 活动
- 日期范围
- 目标
- 发起者（参与者）

除筛选器外，还可搜索特定条目。

![审核日志](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 中的设备管理简介](overview.md)



