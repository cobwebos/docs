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
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262238"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 门户管理设备标识

通过 Azure Active Directory （Azure AD）中的设备标识管理，可以确保用户从满足安全和合规性标准的设备访问资源。

本文：

- 假设你熟悉[中的设备标识管理简介 Azure Active Directory](overview.md)
- 提供有关使用 Azure AD 门户管理设备标识的信息

## <a name="manage-device-identities"></a>管理设备标识

Azure AD 门户提供了一个用于管理设备标识的中心位置。 可以通过使用[直接链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或以下方法来转到此位置：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory** > **设备**。

可在“设备”页上完成以下操作：

- 配置设备设置
- 查找设备
- 执行设备标识管理任务
- 查看与设备相关的审核日志  
  
## <a name="configure-device-settings"></a>配置设备设置

若要使用 Azure AD 门户管理设备标识，你的设备需要[已注册或已加入](overview.md)Azure AD。 作为管理员，可以通过配置设备设置来微调注册和加入设备的过程。

通过 "设备设置" 页，您可以配置与设备标识相关的设置：

![管理 Intune 设备](./media/device-management-azure-portal/21.png)

- **用户可以将设备加入到 Azure AD** -此设置使你能够选择可将其设备注册为 Azure AD 加入设备的用户。 默认值为“全部”。

> [!NOTE]
> **用户可以将设备加入到 Azure AD**设置仅适用于 Windows 10 上的 Azure AD 加入。

- **已加入 Azure AD 设备上的其他本地管理员** - 可选择具有此设备的本地管理员权限的用户。 此处添加的用户会添加到 Azure AD 中的“设备管理员”角色。 默认情况下，Azure AD 中的全局管理员和设备所有者均具有本地管理员权限。 此选项属于高级版功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。
- **用户可能将其设备注册到 Azure AD** -需要配置此设置，以允许向 Azure AD 注册 Windows 10 个人、IOS、Android 和 macOs 设备。 如果选择 "**无**"，则不允许设备注册 Azure AD。 登记到 Microsoft Intune 或 Office 365 移动设备管理 (MDM) 需要进行注册。 如果已配置其中的任一服务，则会选中“全部”且“无”不可用。
- **需要多重身份验证才能加入设备**-可以选择是否要求用户提供附加身份验证因素，以将其设备加入 Azure AD。 默认值为 No。 在注册设备时，建议要求多重身份验证。 为此设备启用多重身份验证前，必须确保已针对注册其设备的用户配置多重身份验证。 有关各种 Azure 多重身份验证服务的详细信息，请参阅 [Azure 多重身份验证入门](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> **需要多重身份验证加入设备**"设置适用于 Azure AD 联接或 Azure AD 注册的设备。 此设置不适用于混合 Azure AD 连接设备。

- **最大设备数**-通过此设置，可以选择用户可以在 Azure AD 中具有的 Azure AD 联接或 Azure AD 注册设备的最大数量。 如果用户达到此配额，则必须先删除一个或多个现有设备，然后才可添加其他设备。 默认值为 20。

> [!NOTE]
> "**最大设备数**" 设置适用于 Azure AD 联接或注册 Azure AD 的设备。 此设置不适用于混合 Azure AD 连接设备。

- **用户可跨设备同步设置和应用数据** - 此设置默认为“无”。 选择特定用户或组，或者选择“全部”，允许用户跨其 Windows 10 设备同步设置和应用数据。 深入了解 Windows 10 中同步的工作原理。
此选项属于高级功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。

## <a name="locate-devices"></a>查找设备

可通过两个选项查找已注册和已加入的设备：

- “设备”页的“管理”部分中的“所有设备”  
- “用户”页的“管理”部分中的“设备”

使用这两个选项均可获得一个具有以下功能的视图：

- 使你能够使用显示名称或设备 ID 作为筛选器来搜索设备。
- 提供有关已注册和已加入设备的详细概述
- 支持执行常见的设备管理任务

![所有设备](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* 如果在 "已注册" 列下看到 "混合 Azure AD 联接" 状态为 "挂起" 的设备，则表示设备已从 Azure AD 连接同步，并正在等待客户端完成注册。 阅读有关如何[计划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)的详细信息。 有关详细信息，请参阅[设备常见问题解答](faq.md)。
>
>   ![挂起的设备](./media/device-management-azure-portal/75.png)
>
>* 对于某些 iOS 设备，包含单引号的设备名可能会使用看起来像单引号的不同字符。 因此搜索此类设备有点棘手：如果您不能正确地看到搜索结果，请确保搜索字符串包含匹配的撇号字符。

## <a name="device-identity-management-tasks"></a>设备标识管理任务

作为全局管理员或云设备管理员，你可以管理已注册或已加入的设备。 Intune 服务管理员可执行以下操作：

- 更新设备 - 例如启用/禁用设备等日常操作
- 删除设备 - 设备停用且应在 Azure AD 中将其删除时

本部分提供有关常见设备标识管理任务的信息。

### <a name="manage-an-intune-device"></a>管理 Intune 设备

Intune 管理员可以管理标记为“Microsoft Intune”的设备。 如果设备未向注册 Microsoft Intune "管理" 选项将灰显。

![管理 Intune 设备](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>启用/禁用 Azure AD 设备

若要启用/禁用设备，可以使用两个选项：

- “所有设备”页上的任务菜单（“...”）

   ![管理 Intune 设备](./media/device-management-azure-portal/71.png)

- “设备”页上的工具栏

   ![管理 Intune 设备](./media/device-management-azure-portal/32.png)

**备注：**

- 若要启用/禁用设备，你必须是中的全局管理员或云设备管理员 Azure AD。 
- 禁用设备会阻止设备在 Azure AD 上成功进行身份验证，从而阻止设备访问由设备 CA 或使用 WH4B 凭据保护的 Azure AD 资源。
- 禁用设备将同时撤消设备上的主刷新令牌（PRT）和任何刷新令牌（RT）。

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

如果设备由另一管理机构（例如 Microsoft Intune）管理，请确保在 Azure AD 中删除设备之前已擦除/停用设备。 删除任何设备之前，请查看如何[管理过时设备](device-management-azure-portal.md)。

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

设备活动通过活动日志提供。 这些日志包括设备注册服务和用户触发的活动：

- 创建设备并在设备上添加所有者/用户
- 更改设备设置
- 删除设备或更新设备等设备操作

审核数据的入口点为“设备”页的“活动”部分中的“审核日志”。

审核日志有一个默认列表视图，其中显示：

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

[如何在 Azure AD 中管理过时设备](manage-stale-devices.md)
