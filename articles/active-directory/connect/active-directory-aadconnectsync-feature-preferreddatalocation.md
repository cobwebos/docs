---
title: "Azure AD Connect 同步：在 Office 365 中为多地域功能配置首选数据位置 | Microsoft Docs"
description: "介绍如何使用 Azure AD Connect 同步将 Office 365 用户资源放在靠近用户的位置。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect 同步：为 Office 365 资源配置首选数据位置
本主题旨在介绍如何在 Azure AD Connect 同步中默认 PreferredDataLocation。当客户在 Office 365 中使用多地域功能时，此属性用于指定用户的 Office 365 数据的地理位置。 条款**区域**和**异地**可以换用。

> [!IMPORTANT]
> 多地域目前以预览版提供。 若要参与预览计划，请与 Microsoft 代表联系。
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>启用 PreferredDataLocation 同步
默认情况下，用户使用的 Office 365 资源位于你的 Azure AD 租户所在区域。 例如，如果你的租户位于北美，则用户的 Exchange 邮箱也位于北美地区。 这对跨国组织而言可能不是最优的情况。 设置属性 preferredDataLocation，可以定义用户的区域。

通过设置此属性，可将用户的 Office 365 资源（例如邮箱和 OneDrive）放在用户所在的同一区域，同时仍对整个组织使用一个租户。

> [!IMPORTANT]
> 若要满足多地域的条件，Office 365 订阅中必须至少有 5000 个席位
>
>

在找不到一份针对 Office 365 提供的所有 Geo[ 其中是你的数据位于](https://aka.ms/datamaps)。

Office 365 中支持多地域的区域包括：

| 地域 | preferredDataLocation 值 |
| --- | --- |
| 亚太区 | APC |
| 澳大利亚 | AUS |
| 加拿大 | CAN |
| 欧盟 | EUR |
| 印度 | IND |
| 日本 | JPN |
| 韩国 | KOR |
| 英国 | GBR |
| 美国 | NAM |

* 如果是地域未列在此表，例如是南美洲，然后它不能为多地域。
* 印度和韩国 geo 才对计费地址和在这些 geo 购买的许可证的客户都可用。
* 并非所有 Office 365 工作负荷都支持设置用户的区域。

Azure AD Connect 支持 1.1.524.0 及更高版本中 **User** 对象的 **PreferredDataLocation** 属性同步。 更具体地讲，已引入以下更改：

* Azure AD 连接器中对象类型 **User** 的架构经过扩展，包含单值字符串类型的 PreferredDataLocation 属性。
* Metaverse 中对象类型 **Person** 的架构经过扩展，包含字符串类型的单值 PreferredDataLocation 属性。

默认情况下，没有为同步启用 PreferredDataLocation 属性。 此功能适用于大型组织，并非每个人都可从中受益。 此外，必须指定一个属性用于保存用户的 Office 365 区域，因为本地 Active Directory 中没有 PreferredDataLocation 属性。 每个组织的此属性都不相同。

> [!IMPORTANT]
> 目前，Azure AD 允许同步用户对象和云用户的 PreferredDataLocation 属性对象要直接使用 Azure AD PowerShell 配置。 启用 PreferredDataLocation 属性同步后，必须停止使用 Azure AD PowerShell 上配置的特性**同步用户对象**作为 Azure AD Connect 将重写它们根据在本地 Active Directory 中的源属性值。

> [!IMPORTANT]
> 从 2017 年 9 月 1 日开始，Azure AD 不再允许直接使用 Azure AD PowerShell 配置**同步用户对象**中的 PreferredDataLocation 属性。 若要在同步的用户对象上配置 PreferredLocation 属性，必须使用 Azure AD Connect。

在启用 PreferredDataLocation 属性同步之前，必须：

* 首先，确定要用作源属性的本地 Active Directory 属性。 它应该采用**单值字符串**类型。 下面的步骤中使用了一个 extensionAttribute。
* 如果之前配置 PreferredDataLocation 属性上现有在使用 Azure AD PowerShell 的 Azure AD 中同步用户对象，则必须**向后移植**本地 Active Directory 中的相应用户对象的属性值。

    > [!IMPORTANT]
    > 如果不向后移植到本地 Active Directory 中的相应用户对象的属性值，Azure AD Connect 会在启用同步 PreferredDataLocation 属性时的 Azure AD 中删除现有的属性值。

* 建议配置的源属性上至少几个本地 AD 用户对象现在，可以用于验证更高版本。

启用 PreferredDataLocation 属性同步的步骤可归纳如下：

1. 禁用同步计划程序，并验证是否没有正在进行的同步操作
2. 将源属性添加到本地 ADDS 连接器架构
3. 将 PreferredDataLocation 添加到 Azure AD 连接器架构
4. 创建流从本地 Active Directory 的属性值的入站的同步规则
5. 创建流到 Azure AD 的属性值的出站同步规则
6. 运行完全同步周期
7. 启用同步计划程序
8. 验证结果

> [!NOTE]
> 本部分的其余部分介绍这些步骤的详细信息。 在 Azure AD 部署使用单林拓扑和不使用自定义同步规则的上下文中描述它们。 如果有多林拓扑、自定义同步规则配置或者过渡服务器，则需要相应地调整步骤。

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步骤 1：禁用同步计划程序，并验证是否没有正在进行的同步操作
确保实现新同步规则的中途不会发生同步，以免将意外的更改导出到 Azure AD。 若要禁用内置的同步计划程序，请执行以下操作：

1. 在 Azure AD Connect 服务器上启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来禁用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $false`
3. 转到“开始” > “同步服务”，启动 Synchronization Service Manager。
4. 转到“操作”选项卡，确认是否不存在状态为“正在进行”的操作。

![Synchronization Service Manager - 检查没有正在进行的操作](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>步骤 2：将源属性添加到本地 ADDS 连接器架构
并非所有 AD 属性都将导入本地 AD 连接器空间。 如果选择使用默认不同步的属性，则需要将其导入。 要将源属性添加到导入属性的列表：

1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
2. 右键单击“本地 AD 连接器”，并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 确保在属性列表中选中你选择使用的源属性。 如果未看到该属性，请单击“全部显示”复选框。
5. 单击“确定”保存。

![将源属性添加到本地 AD 连接器架构](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>步骤 3：将 PreferredDataLocation 添加到 Azure AD 连接器架构
默认情况下，PreferredDataLocation 属性不会导入 Azure AD 连接器空间。 要将 PreferredDataLocation 属性添加到导入属性的列表：

1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
2. 右键单击“Azure AD 连接器”，并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 选择属性列表中的 preferredDataLocation 属性。
5. 单击“确定”保存。

![将源属性添加到 Azure AD 连接器架构](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步骤 4：创建流从本地 Active Directory 的属性值的入站的同步规则
入站同步规则允许要流到 Metaverse 源属性从本地 Active Directory 中的属性值：

1. 转到“开始” > “同步规则编辑器”，启动“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“入站”。
3. 单击“添加新规则”按钮创建新的入站规则。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | --- | --- | --- |
    | 名称 | *提供名称* | 例如，*“In from AD – User PreferredDataLocation”* |
    | 说明 | *提供自定义说明* |  |
    | 连接的系统 | *选择本地 AD 连接器* |  |
    | 连接的系统对象类型 | **User** |  |
    | Metaverse 对象类型 | **Person** |  |
    | 链接类型 | **Join** |  |
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 将“范围筛选器”留空以包括所有对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。
6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    |直接 | PreferredDataLocation | 选择源属性 | 未选中 | 更新 |

7. 单击“添加”创建入站规则。

![创建入站同步规则](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步骤 5：创建流到 Azure AD 的属性值的出站同步规则
出站同步规则允许要在 Azure AD 中从 Metaverse 流向 PreferredDataLocation 属性的属性值：

1. 转到“同步规则”编辑器。
2. 将搜索筛选器的“方向”设置为“出站”。
3. 单击“添加新规则”按钮。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | ----- | ------ | --- |
    | 名称 | *提供名称* | 例如，“Out to AAD – User PreferredDataLocation” |
    | 说明 | *提供说明* ||
    | 连接的系统 | *选择 AAD 连接器* ||
    | 连接的系统对象类型 | 用户 ||
    | Metaverse 对象类型 | **Person** ||
    | 链接类型 | **Join** ||
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加**包含两个子句的单个范围筛选器组**：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 用户 |
    | cloudMastered | NOTEQUAL | True |

    范围筛选器确定要将此出站同步规则应用到哪些 Azure AD 对象。 在本示例中，我们将使用“Out to AD – User Identity”OOB 同步规则中的相同范围筛选器。 它可以防止将同步规则应用到未从本地 Active Directory 同步的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | PreferredDataLocation | PreferredDataLocation | 未选中 | 更新 |

7. 关闭“添加”创建出站规则。

![创建出站同步规则](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>步骤 6：运行完全同步周期
一般情况下，完全同步周期是必需的，因为我们已添加到这两个 AD 的新属性和 Azure AD 连接器架构和引入的自定义同步规则。 建议在将其导出到 Azure AD 之前验证所做的更改。 可使用以下步骤手动运行完全同步周期所构成的步骤时验证更改。

1. 在**本地 AD 连接器**上运行**完全导入**步骤：

   1. 在 Synchronization Service Manager 中转到“操作”选项卡。
   2. 右键单击“本地 AD 连接器”，并选择“运行...”
   3. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   4. 等待操作完成。

    > [!NOTE]
    > 如果源属性已包含在导入属性列表中，则可以在本地 AD 连接器上跳过“完全导入”。 换而言之，不需要在执行[步骤 2：将源属性添加到本地 AD 连接器架构](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema)期间进行任何更改。

2. 在**Azure AD 连接器**上运行**完全导入**步骤：

   1. 右键单击“Azure AD 连接器”，并选择“运行...”
   2. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   3. 等待操作完成。

3. 验证现有 User 对象上的同步规则更改：

源属性已从本地 Active Directory 和 Azure AD 中的 PreferredDataLocation 导入相应的连接器空间。 在继续执行完全同步步骤之前，建议在本地 AD 连接器空间中的现有用户对象上执行“预览”。 选择的对象应具有填充的源属性。 成功**预览**填充 Metaverse 中 PreferredDataLocation 是一个很好的指标，表示已配置同步规则正确。 有关如何执行**预览**的信息，请参阅[验证更改](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change)部分。

4. 在**本地 AD 连接器**上运行**完全同步**步骤：

   1. 右键单击“本地 AD 连接器”，并选择“运行...”
   2. 在弹出对话框中，选择“完全同步”，并单击“确定”。
   3. 等待操作完成。

5. 验证 Azure AD 的**挂起的导出**：

   1. 右键单击“Azure AD连接器”，并选择“搜索连接器空间”。
   2. 在“搜索连接器空间”弹出对话框中：

      1. 将“范围”设置为“挂起的导出”。
      2. 选中所有三个复选框，包括“添加”、“修改”和“删除”。
      3. 单击“搜索”按钮获取要导出其更改的对象列表。 若要检查给定对象的更改，请双击该对象。
      4. 验证更改是否符合需要。

6. 在**Azure AD 连接器**上运行**导出**步骤

   1. 右键单击“Azure AD 连接器”，并选择“运行...”。
   2. 在“运行连接器”弹出对话框中选择“导出”，并单击“确定”。
   3. 等待导出到 Azure AD 完成。

> [!NOTE]
> 可以注意到，这些步骤不包括 Azure AD 的完全同步步骤和 AD 连接器的导出步骤。 由于属性值只会从本地 Active Directory 流向 Azure AD，因此不需要执行这些步骤。

## <a name="step-7-re-enable-sync-scheduler"></a>步骤 7：重新启用同步计划程序
重新启用内置的同步计划程序：

1. 启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来重新启用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>步骤 8：验证结果
现在，可以验证配置并为用户启用该配置。

1. 将区域添加到用户的选定属性。 [此表](#enable-synchronization-of-preferreddatalocation)中提供了可用区域列表。  
![已添加到用户的 AD 属性](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 等待该属性同步到 Azure AD。
3. 使用 Exchange Online PowerShell 验证是否正确设置了邮箱区域。  
![在 Exchange Online 中对用户设置的邮箱区域](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
假设租户已标记为能够使用此功能，则邮箱会移到正确的区域。 可以通过查看邮箱所在的服务器名称进行此项验证。
4. 若要验证此设置是否对多个邮箱生效，请使用 [Technet 库](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)中的脚本。 此脚本还提供所有 Office 365 数据中心服务器的前缀列表及其所在的区域。 可以在上一步骤中参考此信息来验证邮箱的位置。

## <a name="next-steps"></a>后续步骤

**详细了解 Office 365 中的多地域：**

* Ignite 中的多地域会话：https://aka.ms/MultiGeoIgnite
* OneDrive 中的多地域：https://aka.ms/OneDriveMultiGeo
* SharePoint Online 中的多地域：https://aka.ms/SharePointMultiGeo

**详细了解同步引擎中的配置模型：**

* 在 [Understanding Declarative Provisioning](active-directory-aadconnectsync-understanding-declarative-provisioning.md)（了解声明性预配）中了解有关配置模型的详细信息。
* 在 [Understanding Declarative Provisioning Expressions](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)（了解声明性预配表达式）中了解有关表达式语言的详细信息。

**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
