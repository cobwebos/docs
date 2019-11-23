---
title: 如何使用 Azure 门户管理设备 | Microsoft Docs
description: 了解如何使用 Azure 门户管理设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8e94a1b15ad8cd55019f9351c82f58130e472d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420601"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Manage device identities using the Azure portal

With device identity management in Azure Active Directory (Azure AD), you can ensure that your users are accessing your resources from devices that meet your standards for security and compliance.

本文：

- Assumes that you are familiar with the [introduction to device identity management in Azure Active Directory](overview.md)
- Provides you with information about managing your device identities using the Azure AD portal

## <a name="manage-device-identities"></a>管理设备标识

The Azure AD portal provides you with a central place to manage your device identities. 可以使用[直接链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或按照以下手动步骤转到此位置：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. Search for and select **Azure Active Directory** or select it from the **Home** page.

3. 在“管理”部分单击“设备”。

   ![配置设备设置](./media/device-management-azure-portal/74.png)

可在“设备”页上完成以下操作：

- 配置设备设置
- 查找设备
- Perform device identity management tasks
- Review device-related audit logs  
  
## <a name="configure-device-settings"></a>配置设备设置

To manage your device identities using the Azure AD portal, your devices need to be either [registered or joined](overview.md) to Azure AD. 作为管理员，可以通过配置设备设置来微调注册和加入设备的过程。

![配置设备设置](./media/device-management-azure-portal/22.png)

使用设备设置页，可以配置：

![管理 Intune 设备](./media/device-management-azure-portal/21.png)

- **Users may join devices to Azure AD** - This setting enables you to select the users who can register their devices as Azure AD joined devices. 默认值为“全部”。

> [!NOTE]
> **Users may join devices to Azure AD** setting is only applicable to Azure AD join on Windows 10.

- **已加入 Azure AD 设备上的其他本地管理员** - 可选择具有此设备的本地管理员权限的用户。 此处添加的用户会添加到 Azure AD 中的“设备管理员”角色。 默认情况下，Azure AD 中的全局管理员和设备所有者均具有本地管理员权限。 此选项属于高级版功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。
- **Users may register their devices with Azure AD** - You need to configure this setting to allow Windows 10 personal, iOS, Android, and macOs devices to be registered with Azure AD. If you select **None**, devices are not allowed to register with Azure AD. 登记到 Microsoft Intune 或 Office 365 移动设备管理 (MDM) 需要进行注册。 如果已配置其中的任一服务，则会选中“全部”且“无”不可用。
- **Require Multi-Factor Auth to join devices** - You can choose whether users are required to provide an additional authentication factor to join their device to Azure AD. 默认值为“否”。 在注册设备时，建议要求多重身份验证。 为此设备启用多重身份验证前，必须确保已针对注册其设备的用户配置多重身份验证。 有关各种 Azure 多重身份验证服务的详细信息，请参阅 [Azure 多重身份验证入门](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> **Require Multi-Factor Auth to join devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Maximum number of devices** - This setting enables you to select the maximum number of Azure AD joined or Azure AD registered devices that a user can have in Azure AD. 如果用户达到此配额，则必须先删除一个或多个现有设备，然后才可添加其他设备。 默认值为 20。

> [!NOTE]
> **Maximum number of devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **用户可跨设备同步设置和应用数据** - 此设置默认为“无”。 选择特定用户或组，或者选择“全部”，允许用户跨其 Windows 10 设备同步设置和应用数据。 深入了解 Windows 10 中同步的工作原理。
此选项属于高级功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。

## <a name="locate-devices"></a>查找设备

可通过两个选项查找已注册和已加入的设备：

- “设备”页的“管理”部分中的“所有设备”  

   ![所有设备](./media/device-management-azure-portal/41.png)

- “用户”页的“管理”部分中的“设备”

   ![所有设备](./media/device-management-azure-portal/43.png)

使用这两个选项均可获得一个具有以下功能的视图：

- Enables you to search for devices using the display name or device ID as filter.
- 提供有关已注册和已加入设备的详细概述
- 支持执行常见的设备管理任务

![所有设备](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* If you see a device that is "Hybrid Azure AD joined" with a state "Pending" under the REGISTERED column, it indicates that the device has been synchronized from Azure AD connect and is waiting to complete registration from the client. Read more on how to [plan your Hybrid Azure AD join implementation](hybrid-azuread-join-plan.md). Additional information can be found in the article, [Devices frequently asked questions](faq.md).
>
>   ![Pending devices](./media/device-management-azure-portal/75.png)
>
>* 对于某些 iOS 设备，包含单引号的设备名可能会使用看起来像单引号的不同字符。 So searching for such devices is a little tricky - if you are not seeing search results correctly, ensure that the search string contains matching apostrophe character.

## <a name="device-identity-management-tasks"></a>Device identity management tasks

As a global administrator or cloud device administrator, you can manage the registered or joined devices. Intune 服务管理员可执行以下操作：

- 更新设备 - 例如启用/禁用设备等日常操作
- 删除设备 - 设备停用且应在 Azure AD 中将其删除时

This section provides you with information about common device identity management tasks.

### <a name="manage-an-intune-device"></a>管理 Intune 设备

Intune 管理员可以管理标记为“Microsoft Intune”的设备。 If the device is not enrolled with Microsoft Intune the "Manage" option will be greyed out.

![管理 Intune 设备](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>启用/禁用 Azure AD 设备

若要启用/禁用设备，可以使用两个选项：

- “所有设备”页上的任务菜单（“...”）

   ![管理 Intune 设备](./media/device-management-azure-portal/71.png)

- “设备”页上的工具栏

   ![管理 Intune 设备](./media/device-management-azure-portal/32.png)

**备注：**

- You need to be a global administrator or cloud device administrator in Azure AD to enable / disable a device. 
- Disabling a device prevents a device from successfully authenticating with Azure AD, thereby preventing the device from accessing your Azure AD resources that are guarded by device CA or using your WH4B credentials.
- Disabling the device will revoke both the Primary Refresh Token (PRT) and any Refresh Tokens (RT) on the device.

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

If a device is managed by another management authority (for example, Microsoft Intune), make sure that the device has been wiped / retired before deleting the device in Azure AD. Review how to [manage stale devices](device-management-azure-portal.md) before deleting any devices.

### <a name="view-or-copy-device-id"></a>查看或复制设备 ID

可以使用设备 ID 在设备上验证设备 ID 详细信息或在故障排除期间使用 PowerShell。 要访问复制选项，请单击设备。

![查看设备 ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>查看或复制 BitLocker 密钥

你可以查看和复制 BitLocker 密钥，帮助用户恢复其加密的驱动器。 这些密钥仅适用于已加密并将其密钥存储在 Azure AD 中的 Windows 设备。 访问设备的详细信息时，可以复制这些密钥。

![查看 BitLocker 密钥](./media/device-management-azure-portal/36.png)

若要查看或复制 BitLocker 密钥，你需要是设备所有者或者是至少分配了以下一个角色的用户：

- 云设备管理员
- 全局管理员角色
- 支持管理员
- Intune 服务管理员
- 安全管理员
- 安全读取者

> [!NOTE]
> 加入混合 Azure AD 的 Windows 10 设备没有所有者。 因此，如果按所有者查找设备未找到它，请按设备 ID 搜索。

## <a name="audit-logs"></a>审核日志

设备活动通过活动日志提供。 These logs include activities triggered by the device registration service and by users:

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
- 确定目标
- 发起者（参与者）

除筛选器外，还可搜索特定条目。

![审核日志](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>后续步骤

[How to manage stale devices in Azure AD](manage-stale-devices.md)
