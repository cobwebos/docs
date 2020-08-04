---
title: 如何使用 Azure 门户管理设备 | Microsoft Docs
description: 了解如何使用 Azure 门户管理设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce09bd2a3f5f474ad5c6e6eb73865e2b2dc9fe3a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541936"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 门户管理设备标识

Azure AD 提供了一个用于管理设备标识的中心位置。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory**  >  **设备**"。

[![Azure 门户中的 "所有设备" 视图](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

通过 "**所有设备**" 页，您可以：

- 确定设备，包括：
   - 已加入或注册 Azure AD 的设备。
   - 使用[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)部署的设备。
   - 使用[通用打印](https://docs.microsoft.com/universal-print/fundamentals/universal-print-getting-started)的打印机
- 执行 "启用"、"禁用"、"删除" 或 "管理" 等设备身份管理任务。
   - 在 Azure AD 中，[打印机](/universal-print/fundamentals/)和[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)设备的管理选项有限。 它们必须从各自的管理界面进行管理。
- 配置设备标识设置。
- 启用或禁用企业状态漫游。
- 查看与设备相关的审核日志

## <a name="manage-devices"></a>管理设备

在 Azure AD 中，有两个管理设备的位置：

- **Azure 门户**  > **Azure Active Directory**  > **设备**
- **Azure 门户**  > **Azure Active Directory**  > **用户**> 选择用户 >**设备**

这两个选项使管理员能够：

- 搜索设备。
- 请参阅设备详细信息，包括：
    - 设备名称
    - 设备 ID
    - 操作系统和版本
    - 联接类型
    - 所有者
    - 移动设备管理和合规性
    - BitLocker 恢复密钥
- 执行设备标识管理任务，如、启用、禁用、删除或管理。
   - 在 Azure AD 中，[打印机](/universal-print/fundamentals/)和[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)设备的管理选项有限。 它们必须从各自的管理界面进行管理。

> [!TIP]
> - 加入混合 Azure AD 的 Windows 10 设备没有所有者。 如果你正在按所有者查找设备，并且找不到它，请按设备 ID 进行搜索。
>
> - 如果在 "已注册" 列下看到 "混合 Azure AD 联接" 状态为 "挂起" 的设备，则表示设备已从 Azure AD 连接同步，并正在等待客户端完成注册。 阅读有关如何[计划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)的详细信息。 有关详细信息，请参阅[设备常见问题解答](faq.md)。
>
> - 对于某些 iOS 设备，包含单引号的设备名可能会使用看起来像单引号的不同字符。 因此搜索此类设备有点棘手：如果您不能正确地看到搜索结果，请确保搜索字符串包含匹配的撇号字符。

### <a name="manage-an-intune-device"></a>管理 Intune 设备

如果你是 Intune 管理员，则可以管理将 MDM 标记**Microsoft Intune**的设备。 如果设备未注册到 Microsoft Intune，则 "管理" 选项将灰显。

### <a name="enable-or-disable-an-azure-ad-device"></a>启用或禁用 Azure AD 设备

若要启用或禁用设备，可以使用两个选项：

- 选择一台或多台设备后，"**所有设备**" 页上的工具栏。
- 向下钻取到特定设备后使用工具栏。

> [!IMPORTANT]
> - 您必须是中的全局管理员或云设备管理员，才能启用或禁用设备 Azure AD。 
> - 禁用设备会阻止设备在 Azure AD 上成功进行身份验证，从而阻止设备访问通过基于设备的条件访问或 Windows Hello 企业版凭据保护的 Azure AD 资源。
> - 禁用设备将同时撤消设备上的主刷新令牌（PRT）和任何刷新令牌（RT）。
> - 无法在 Azure AD 中启用或禁用打印机。

### <a name="delete-an-azure-ad-device"></a>删除 Azure AD 设备

若要删除设备，可以使用两个选项：

- 选择一台或多台设备后，"**所有设备**" 页上的工具栏。
- 向下钻取到特定设备后使用工具栏。

> [!IMPORTANT]
> - 你必须在 Azure AD 中为云设备管理员、Intune 管理员或全局管理员角色分配，才能删除设备。
> - 无法在 Azure AD 中删除打印机和 Windows Autopilot 设备
> - 删除设备：
>    - 可阻止设备访问你的 Azure AD 资源。
>    - 可删除附加到设备的所有详细信息，例如适用于 Windows 设备的 BitLocker 密钥。  
>    - 表示一个不可恢复的活动，除非必需，否则不建议。

如果设备由另一管理机构（例如 Microsoft Intune）管理，请确保在 Azure AD 中删除设备之前已擦除/停用设备。 删除任何设备之前，请查看如何[管理过时设备](manage-stale-devices.md)。

### <a name="view-or-copy-device-id"></a>查看或复制设备 ID

可以使用设备 ID 在设备上验证设备 ID 详细信息或在故障排除期间使用 PowerShell。 要访问复制选项，请单击设备。

![查看设备 ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>查看或复制 BitLocker 密钥

您可以查看和复制 BitLocker 密钥，以允许用户恢复加密的驱动器。 这些密钥仅适用于已加密并将其密钥存储在 Azure AD 中的 Windows 设备。 通过选择 "**显示恢复密钥**" 访问设备的详细信息时，可以找到这些密钥。 选择 "**显示恢复密钥**" 将生成一个审核日志，您可以在该类别中找到该日志 `KeyManagement` 。

![查看 BitLocker 密钥](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

若要查看或复制 BitLocker 密钥，你需要是设备所有者或者是至少分配了以下一个角色的用户：

- 云设备管理员
- 全局管理员角色
- 支持管理员
- Intune 服务管理员
- 安全管理员
- 安全读取者

### <a name="device-list-filtering-preview"></a>设备列表筛选（预览）

以前，只能按活动和已启用状态筛选设备列表。 此预览版现在允许你按设备上的下列属性筛选设备列表：

- 启用状态
- 相容状态
- 联接类型（Azure AD 联接、混合 Azure AD 联接、Azure AD 注册）
- 活动时间戳
- 操作系统
- 设备类型（打印机、安全 Vm、共享设备、已注册的设备）

若要在 "**所有设备**" 视图中启用预览筛选功能：

![启用筛选预览功能](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory**  >  **设备**"。
1. 选择显示的标题，**尝试新的设备筛选改进。单击以启用预览。**

你现在可以**将筛选器添加**到 "**所有设备**" 视图。

## <a name="configure-device-settings"></a>配置设备设置

若要使用 Azure AD 门户管理设备标识，需要将这些设备[注册或联接](overview.md)到 Azure AD。 作为管理员，你可以通过配置以下设备设置来控制注册和加入设备的过程。

![与 Azure AD 相关的设备设置](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **用户可以将设备加入到 Azure AD** -此设置使你能够选择可将其设备注册为 Azure AD 加入设备的用户。 默认值是 **All**。

> [!NOTE]
> **用户可以将设备加入到 Azure AD**设置仅适用于 Windows 10 上的 Azure AD 加入。

- **已加入 Azure AD 设备上的其他本地管理员** - 可选择具有此设备的本地管理员权限的用户。 将这些用户添加到 Azure AD 中的 "*设备管理员*" 角色。 默认情况下，Azure AD 中的全局管理员和设备所有者均具有本地管理员权限。 此选项属于高级版功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。
- **用户可能将其设备注册到 Azure AD** -需要配置此设置，以允许向 Azure AD 注册 Windows 10 个人、IOS、Android 和 macOS 设备。 如果选择 "**无**"，则不允许设备注册 Azure AD。 登记到 Microsoft Intune 或 Office 365 移动设备管理 (MDM) 需要进行注册。 如果已配置其中的任一服务，则会选中“全部”且“无”不可用********。
- **需要多重身份验证才能加入设备**-可以选择是否要求用户提供附加身份验证因素，以将其设备加入 Azure AD。 默认值为 No****。 在注册设备时，建议要求多重身份验证。 为此设备启用多重身份验证前，必须确保已针对注册其设备的用户配置多重身份验证。 有关各种 Azure 多重身份验证服务的详细信息，请参阅 [Azure 多重身份验证入门](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> **需要多重身份验证加入设备**"设置适用于 Azure AD 联接或 Azure AD 注册的设备。 此设置不适用于混合 Azure AD 连接设备。

- **最大设备数**-通过此设置，可以选择用户可以在 Azure AD 中具有的 Azure AD 联接或 Azure AD 注册设备的最大数量。 如果用户达到此配额，则必须先删除一个或多个现有设备，然后才可添加其他设备。 默认值为**20**。

> [!NOTE]
> "**最大设备数**" 设置适用于 Azure AD 联接或注册 Azure AD 的设备。 此设置不适用于混合 Azure AD 连接设备。

- [企业状态漫游](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>审核日志

设备活动通过活动日志提供。 这些日志包括设备注册服务和用户触发的活动：

- 创建设备并在设备上添加所有者/用户
- 更改设备设置
- 删除设备或更新设备等设备操作

审核数据的入口点为“设备”页的“活动”部分中的“审核日志”************。

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

[企业状态漫游](enterprise-state-roaming-overview.md)
