---
title: "Azure AD Connect 同步：在 Azure AD Connect 同步中进行配置更改 | Microsoft 文档"
description: "介绍如何对 Azure AD Connect 同步中的配置进行更改。"
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: billmath
ms.openlocfilehash: 1fd07d506b2edc789d71001ac520b9ebddc3e1d9
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect 同步：如何更改默认配置
本主题旨在介绍如何对 Azure AD Connect 同步中的默认配置进行更改。其中提供了一些常见方案的步骤。 了解这些知识后，用户应该能够根据自己的业务规则对自己的配置进行一些简单的更改。

## <a name="synchronization-rules-editor"></a>同步规则编辑器
同步规则编辑器用于查看和更改默认配置。 可以在 **Azure AD Connect** 组下的开始菜单中找到它。  
![具有同步规则编辑器的开始菜单](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

打开时，将看到现成可用的默认规则。

![同步规则编辑器](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>在编辑器中导航
通过编辑器顶部的下拉列表可以快速查找特定规则。 例如，如果想要查看包含属性 proxyAddresses 的规则，可将下拉列表更改为：  
![SRE 筛选](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
若要重置筛选并加载新配置，请按键盘上的 **F5**。

右上角上有一个“添加新规则”按钮。 此按钮用于创建自定义规则。

顶部有用于执行所选同步规则的按钮。 “编辑”和“删除”按钮可执行其应有的操作。 “导出”按钮可生成用于创建同步规则的 PowerShell 脚本。 在此过程中，用户可以将同步规则从一台服务器移到另一台。

## <a name="create-your-first-custom-rule"></a>创建第一个自定义规则
最常见的更改是对属性流的更改。 源目录中的数据可能与 Azure AD 中的不同。 在本部分的示例中，需要确保用户的给定名称始终为**首字母大写**。

### <a name="disable-the-scheduler"></a>禁用计划程序
默认情况下，[计划程序](active-directory-aadconnectsync-feature-scheduler.md)每 30 分钟运行一次。 进行更改以及排查新规则错误时，需要确保其未启动。 要临时禁用计划程序，请启动 PowerShell，并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`

![禁用计划程序](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>创建规则
1. 单击“添加新规则”。
2. 在“说明”页上输入以下内容：  
   ![入站规则筛选](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * 名称：为规则提供说明性名称。
   * 说明：提供一些说明以便他人可以理解规则的用途。
   * 连接的系统：可从中找到对象的系统。 这种情况下，请选择 Active Directory 连接器。
   * 连接的系统/Metaverse 对象类型：分别选择“用户”和“人员”。
   * 链接类型：将该值更改为“联接”。
   * 优先级：提供系统中唯一的值。 较低的数值表示较高的优先级。
   * 标记：留空。 只有 Microsoft 中现成可用的规则应该会要求在此框中填入值。
3. 在“范围筛选器”页上，输入“givenName ISNOTNULL”。  
   ![入站规则范围筛选器](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   此部分用于定义规则应该应用到哪些对象。 如果留空，该规则会应用到所有用户对象。 但也可包括会议室、服务帐户和其他非个人用户对象。
4. 在“联接规则”上，将其留空。
5. 在“转换”页上，将 FlowType 更改为 **Expression**。 选择目标属性 **givenName**，在“源”中输入 `PCase([givenName])`。
   ![入站规则转换](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   函数名称和属性名称上的同步引擎要区分大小写。 如果键入出错，则添加规则时会看到警告。 编辑器允许保存并继续，因此必须重新打开规则并进行更正。
6. 单击“添加”保存规则。

新的自定义规则应该与系统中的其他同步规则一起显示。

### <a name="verify-the-change"></a>验证更改
采用此新更改后，需要确保其按预期方式工作并且不会引发任何错误。 根据拥有的对象数量，有两种不同的方法执行此步骤。

1. 在所有对象上运行完全同步
2. 在单个对象上运行预览和完全同步

从“开始”菜单启动“同步服务”。 本部分中的步骤全部在此工具中。

1. **针对所有对象的完全同步**  
   选择顶部的“连接器”。 标识在前面部分中进行过更改的连接器（在本例中为 Active Directory 域服务），并选中它。 从“操作”中选择“运行”，并选择“完全同步”和“确定”。
   ![完全同步](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   现已更新了 metaverse 中的对象。 用户想要查看 metaverse 中的对象。
2. **在单个对象上的预览和完全同步**  
   选择顶部的“连接器”。 标识在前面部分中进行过更改的连接器（在本例中为 Active Directory 域服务），并选中它。 选择“搜索连接器空间”。 使用作用域来查找想要用于测试更改的对象。 选择该对象，并单击“预览”。 在新的屏幕中，选择“提交预览”。  
   ![提交预览](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   现已将更改提交到 metaverse。

**查看 metaverse 中的对象**  
用户要挑选几个示例对象，确保值符合预期并且已应用规则。 从顶部选择“Metaverse 搜索”。 添加查找相关对象所需的筛选器。 从搜索结果中，打开对象。 查看属性值，同时还要在按预期方式应用规则的“同步规则”列中进行验证。  
![Metaverse 搜索](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>启用计划程序
如果一切按预期方式进行，可以再次启用计划程序。 从 PowerShell 中运行 `Set-ADSyncScheduler -SyncCycleEnabled $true`。

## <a name="other-common-attribute-flow-changes"></a>其他常见的属性流更改
前面部分介绍了如何更改属性流。 本部分提供了另外一些示例。 虽然创建同步规则的步骤已缩简，但可以在前面部分中找到完整步骤。

### <a name="use-another-attribute-than-the-default"></a>使用其他属性而不是默认属性
Fabrikam 中有对名字、姓氏和显示名称使用本地字母的林。 以拉丁字母表示的这些属性可在扩展属性中找到。 在 Azure AD 和 Office 365 中创建全局地址列表时，组织反而想要使用这些属性。

使用默认配置时，本地林中的对象如下所示：  
![属性流 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

若要使用其他属性流创建规则，请执行以下操作：

* 从开始菜单启动“同步规则编辑器”。
* 在左侧依然选定了“入站”的情况下，单击“添加新规则”按钮。
* 为规则指定名称和说明。 选择本地 Active Directory 和相关的对象类型。 在“链接类型”中选择“联接”。 为优先级选择一个未被其他规则使用的数字。 现成的规则从 100 开始，因此该示例可以使用值 50。
  ![属性流 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* 将范围留空（即应该应用到林中的所有用户对象）。
* 将联接规则留空（即让现成的规则处理所有联接）。
* 在“转换”中创建以下流：  
  ![属性流 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* 单击“添加”保存规则。
* 转到“同步服务管理器”。 在“连接器”上，选择我们已在其中添加了规则的“连接器”。 选择“运行”和“完全同步”。 完全同步将使用当前规则重新计算所有对象。

这是使用此自定义规则的同一对象的结果：  
![属性流 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>属性的长度
字符串属性在默认情况下设置为可编制索引，并且最大长度为 448 个字符。 如果正在使用其中可能包含更多字符的字符串属性，请确保属性流中包括以下内容：  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>更改 userPrincipalSuffix
Active Directory 中的 userPrincipalName 属性并非始终被用户知晓，并且可能不适合作为登录 ID。 Azure AD Connect 同步安装向导允许选择不同的属性，例如 mail。 但在某些情况下，必须计算该属性。 例如：公司 Contoso 具有两个 Azure AD 目录，一个用于生产，另一个用于测试。 他们希望测试租户中的用户使用登录 ID 中的另一后缀。  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

在此表达式中，使用第一个 @-sign (Word) 的左侧所有内容，并与固定字符串连接。

### <a name="convert-a-multi-value-to-a-single-value"></a>将多值转换为单值
Active Directory 中的某些属性在架构中是多值，不过它们在 Active Directory 用户和计算机中看上去是单值。 一个示例就是说明属性。  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

在此表达式中，如果属性具有值，请使用属性中的第一项 (Item)，删除前导空格和尾随空格 (Trim)，并保留字符串中的前 448 个字符（左）。

### <a name="do-not-flow-an-attribute"></a>不要流送属性
有关本部分方案的背景信息，请参阅[控制属性流过程](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process)。

有两种方法可防止流送属性。 第一种方法可在安装向导中使用，允许用户[删除选定的属性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。 如果以前未曾同步该属性，则可以使用这个选项。 但是，如果已开始同步此属性，后来使用此功能将它删除，则同步引擎将停止管理属性，现有值将保留在 Azure AD 中。

如果想要删除某个属性的值并确保将来不会流送该属性，则需要改为创建自定义规则。

在 Fabrikam 上，我们在同步到云的属性中发现了一些不应该存在的属性。 我们希望确保从 Azure AD 中删除这些属性。  
![错误的扩展属性](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* 创建新的入站同步规则并填充说明 ![说明](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* 创建类型为 **Expression** 且源为 **AuthoritativeNull** 的属性流。 即使优先顺序较低的同步规则尝试填充值，文本值“AuthoritativeNull”也会指出 MV 中的值应该为空。
  ![扩展属性的转换](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* 保存同步规则。 启动“同步服务”，查找“连接器”，并依次选择“运行”和“完全同步”。 此步骤将重新计算所有属性流。
* 通过搜索连接器空间来验证是否即将导出所需的更改。
  ![分阶段删除](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>使用 PowerShell 创建规则
如只需进行少量更改，使用同步规则编辑器即可达到目的。 如需进行大量更改，最好选择 PowerShell。 某些高级功能只有 PowerShell 中才会提供。

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>获取现成规则的 PowerShell 脚本
要查看创建现成规则的 PowerShell 脚本，在同步规则编辑器中选择此规则，并单击“导出”。 此操作会提供创建该规则的 PowerShell 脚本。

### <a name="advanced-precedence"></a>高级优先级
以 100 优先级值开头的现成同步规则。 如果有多个林，并且需要进行大量自定义更改，那么 99 个同步规则可能不够。

可以指示同步引擎需要在现成规则前插入其他规则。 若要获取此行为，请执行以下步骤：

1. 在同步规则编辑器中标记第一个现成同步规则（此规则为 **In from AD-User Join**），并选择“导出”。 复制 SR 标识符值。  
![更改前的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. 创建新的同步规则。 可使用同步规则编辑器创建它。 将规则导出到 PowerShell 脚本。
3. 在 **PrecedenceBefore** 属性中，从现成规则插入标识符值。 将“优先级”设置为“0”。 请确保标识符属性唯一，且不会从另一规则中重复使用某个 GUID。 此外，请确保未设置 **ImmutableTag** 属性，仅为现成规则设置该属性。 保存 PowerShell 脚本并运行它。 这样就会为自定义规则分配优先级值 100，且所有其他现成规则优先级值随之递增。  
![更改后的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

必要时，可让多个自定义同步规则使用相同的 **PrecedenceBefore** 值。

## <a name="enable-synchronization-of-preferreddatalocation"></a>启用 PreferredDataLocation 同步
默认情况下，用户使用的 Office 365 资源位于你的 Azure AD 租户所在区域。 例如，如果你的租户位于北美，则用户的 Exchange 邮箱也位于北美地区。 这对跨国组织而言可能不是最优的情况。 设置属性 preferredDataLocation，可以定义用户的区域。

Office 365 中的区域有：

| 区域 | 说明 |
| --- | --- |
| NAM | 北美 |
| EUR | 欧洲 |
| APC | 亚太区 |
| JPN | 日本 |
| AUS | 澳大利亚 |
| CAN | 加拿大 |
| GBR | 英国 |
| LAM | 拉丁美洲 |

并非所有 Office 365 工作负荷都支持设置用户的区域。

Azure AD Connect 支持 1.1.524.0 及更高版本中 **User** 对象的 **PreferredDataLocation** 属性同步。 更具体地讲，已引入以下更改：

* Azure AD 连接器中对象类型 **User** 的架构经过扩展，包含单值字符串类型的 PreferredDataLocation 属性。

* Metaverse 中对象类型 **Person** 的架构经过扩展，包含字符串类型的单值 PreferredDataLocation 属性。

默认情况下，PreferredDataLocation 属性未启用同步，因为在本地 Active Directory 中没有相应的 PreferredDataLocation 属性。 必须手动启用同步。

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
2. 将源属性添加到本地 AD 连接器架构
3. 将 PreferredDataLocation 添加到 Azure AD 连接器架构
4. 创建流从本地 Active Directory 的属性值的入站的同步规则
5. 创建流到 Azure AD 的属性值的出站同步规则
6. 运行完全同步周期
7. 启用同步计划程序

> [!NOTE]
> 本部分的其余部分介绍这些步骤的详细信息。 在 Azure AD 部署使用单林拓扑和不使用自定义同步规则的上下文中描述它们。 如果有多林拓扑、自定义同步规则配置或者过渡服务器，则需要相应地调整步骤。

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步骤 1：禁用同步计划程序，并验证是否没有正在进行的同步操作
确保实现新同步规则的中途不会发生同步，以免将意外的更改导出到 Azure AD。 若要禁用内置的同步计划程序，请执行以下操作：

1. 在 Azure AD Connect 服务器上启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来禁用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $false`
3. 转到“开始” > “同步服务”，启动 Synchronization Service Manager。
4. 转到“操作”选项卡，确认是否不存在状态为“正在进行”的操作。

![Synchronization Service Manager - 检查没有正在进行的操作](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>步骤 2：将源属性添加到本地 AD 连接器架构
并非所有 AD 属性都将导入本地 AD 连接器空间。 如果选择使用默认不同步的属性，则需要将其导入。 要将源属性添加到导入属性的列表：

1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
2. 右键单击“本地 AD 连接器”，并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 确保在属性列表中选中你选择使用的源属性。
5. 单击“确定”保存。

![将源属性添加到本地 AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>步骤 3：将 PreferredDataLocation 添加到 Azure AD 连接器架构
默认情况下，PreferredDataLocation 属性不会导入 Azure AD 连接器空间。 要将 PreferredDataLocation 属性添加到导入属性的列表：

1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
2. 右键单击“Azure AD 连接器”，并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 选择属性列表中的 PreferredDataLocation 属性。
5. 单击“确定”保存。

![将源属性添加到 Azure AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步骤 4：创建流从本地 Active Directory 的属性值的入站的同步规则
入站同步规则允许要流到 Metaverse 源属性从本地 Active Directory 中的属性值：

1. 转到“开始” > “同步规则编辑器”，启动“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“入站”。
3. 单击“添加新规则”按钮创建新的入站规则。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | --- | --- | --- |
    | 名称​​ | *提供名称* | 例如，*“In from AD – User PreferredDataLocation”* |
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

![创建入站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步骤 5：创建流到 Azure AD 的属性值的出站同步规则
出站同步规则允许要在 Azure AD 中从 Metaverse 流向 PreferredDataLocation 属性的属性值：

1. 转到“同步规则”编辑器。
2. 将搜索筛选器的“方向”设置为“出站”。
3. 单击“添加新规则”按钮。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | ----- | ------ | --- |
    | 名称​​ | *提供名称* | 例如，“Out to AAD – User PreferredDataLocation” |
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

![创建出站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>步骤 6：运行完全同步周期
一般情况下，完全同步周期是必需的，因为我们已添加到这两个 AD 的新属性和 Azure AD 连接器架构和引入的自定义同步规则。 建议在将其导出到 Azure AD 之前验证所做的更改。 可使用以下步骤手动运行完全同步周期所构成的步骤时验证更改。

1. 在**本地 AD 连接器**上运行**完全导入**步骤：

   1. 在 Synchronization Service Manager 中转到“操作”选项卡。

   2. 右键单击“本地 AD 连接器”，并选择“运行...”

   3. 在弹出对话框中，选择“完全导入”，并单击“确定”。

   4. 等待操作完成。

    > [!NOTE]
    > 如果源属性已包含在导入属性列表中，则可以在本地 AD 连接器上跳过“完全导入”。 换而言之，不需要在执行[步骤 2：将源属性添加到本地 AD 连接器架构](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)期间进行任何更改。

2. 在**Azure AD 连接器**上运行**完全导入**步骤：

   1. 右键单击“Azure AD 连接器”，并选择“运行...”

   2. 在弹出对话框中，选择“完全导入”，并单击“确定”。

   3. 等待操作完成。

3. 验证现有 User 对象上的同步规则更改：

源属性已从本地 Active Directory 和 Azure AD 中的 PreferredDataLocation 导入相应的连接器空间。 在继续执行完全同步步骤之前，建议在本地 AD 连接器空间中的现有用户对象上执行“预览”。 选择的对象应具有填充的源属性。 成功**预览**填充 Metaverse 中 PreferredDataLocation 是一个很好的指标，表示已配置同步规则正确。 有关如何执行**预览**的信息，请参阅[验证更改](#verify-the-change)部分。

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

### <a name="step-7-re-enable-sync-scheduler"></a>步骤 7：重新启用同步计划程序
重新启用内置的同步计划程序：

1. 启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来重新启用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="enable-synchronization-of-usertype"></a>启用 UserType 同步
Azure AD Connect 支持 1.1.524.0 及更高版本中 User 对象的 UserType 属性同步。 更具体地讲，已引入以下更改：

- Azure AD 连接器中对象类型 User 的架构经过扩展，包含字符串类型的单值 UserType 属性。
- Metaverse 中对象类型 User 的架构经过扩展，包含字符串类型的单值 Person 属性。

默认情况下，UserType 属性未启用同步，因为在本地 Active Directory 中没有相应的 UserType 属性。 必须手动启用同步。 启用 UserType 属性同步之前，必须记录 Azure AD 强制实施的以下行为：

- Azure AD 只接受 UserType 属性的两个值 – **Member** 和 **Guest**
- 如果没有在 Azure AD Connect 中启用 UserType 属性同步，则通过目录同步创建的 Azure AD 用户的 UserType 属性将设置为 **Member**。
- Azure AD 不允许 Azure AD Connect 更改现有 Azure AD 用户的 UserType 属性。 该属性只能在 Azure AD 用户创建过程中设置。

在启用 UserType 属性同步之前，必须首先确定如何从本地 AD 派生 UserType 属性。 两种常见方法包括：

- 指定要用作源属性的尚未使用的本地 AD 属性（例如 extensionAttribute1）。 指定的本地 AD 属性应为字符串类型，具有单值，且包含值 Member 或 Guest。 如果选择此方法，则在启用 UserType 属性同步之前，对于同步到 Azure AD 的本地 Active Directory 中的所有现有用户对象，必须确保指定的属性填充了正确的值。
- 或者，可以从其他属性派生 UserType 属性的值。 例如，如果用户的本地 AD UserPrincipalName 属性结尾是域部分“@partners.fabrikam123.org”，则应将这些用户全部同步为 Guest。 如前所述，Azure AD Connect 不允许 Azure AD Connect 更改现有 Azure AD 用户的 UserType 属性。 因此，对于租户中的所有 Azure AD 用户，必须确保采用的逻辑与 UserType 属性的现有配置方式一致。

启用 UserType 属性同步的步骤可归纳如下：

>[!NOTE]
> 本节其余部分介绍了这些步骤。 在 Azure AD 部署使用单林拓扑和不使用自定义同步规则的上下文中描述它们。 如果有多林拓扑、自定义同步规则配置或者过渡服务器，则需要相应地调整步骤。

1.  禁用同步计划程序，并验证是否没有正在进行的同步操作
2.  将源属性添加到本地 AD 连接器架构
3.  将 UserType 添加到 Azure AD 连机器架构
4.  创建流从本地 Active Directory 的属性值的入站的同步规则
5.  创建流到 Azure AD 的属性值的出站同步规则
6.  运行完全同步周期
7.  启用同步计划程序


### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步骤 1：禁用同步计划程序，并验证是否没有正在进行的同步操作
确保实现新同步规则的中途不会发生同步，以免将意外的更改导出到 Azure AD。 若要禁用内置的同步计划程序，请执行以下操作：

 1. 在 Azure AD Connect 服务器上启动 PowerShell 会话。
 2. 通过运行以下 cmdlet 来禁用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $false`
 3. 转到“开始”→“同步服务”，启动“Synchronization Service Manager”。
 4. 转到“操作”选项卡，确认是否不存在状态为“正在进行”的操作。

![Synchronization Service Manager - 检查没有正在进行的操作](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>步骤 2：将源属性添加到本地 AD 连接器架构
并非所有 AD 属性都将导入本地 AD 连接器空间。 要将源属性添加到导入属性的列表：

 1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
 2. 右键单击“本地 AD 连接器”，并选择“属性”。
 3. 在弹出对话框中，转到“选择属性”选项卡。
 4. 确保在属性列表中选中源属性。
 5. 单击“确定”保存。
![将源属性添加到本地 AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-usertype-to-the-azure-ad-connector-schema"></a>第 3 步：将 UserType 添加到 Azure AD 连机器架构
默认情况下，UserType 属性不会导入 Azure AD 连接空间。 将 UserType 属性添加到导入属性的列表：

 1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
 2. 右键单击“Azure AD 连接器”，并选择“属性”。
 3. 在弹出对话框中，转到“选择属性”选项卡。
 4. 确保在属性列表中选中 PreferredDataLocation 属性。
 5. 单击“确定”保存。

![将源属性添加到 Azure AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步骤 4：创建流从本地 Active Directory 的属性值的入站的同步规则
入站同步规则允许要流到 Metaverse 源属性从本地 Active Directory 中的属性值：

1. 转到“开始”→“同步规则编辑器”，启动“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“入站”。
3. 单击“添加新规则”按钮创建新的入站规则。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | --- | --- | --- |
    | 名称​​ | *提供名称* | 例如 “In from AD – User UserType” |
    | 说明 | *提供说明* |  |
    | 连接的系统 | *选择本地 AD 连接器* |  |
    | 连接的系统对象类型 | **User** |  |
    | Metaverse 对象类型 | **Person** |  |
    | 链接类型 | **Join** |  |
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加**包含以下子句的单个范围筛选器组**：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | 用户\_ |

    范围筛选器确定要将此入站同步规则应用到哪些本地 AD 对象。 在本示例中，我们将使用用作“In from AD – User Common”OOB 同步规则的相同范围筛选器，防止将同步规则应用到通过 Azure AD 用户写回功能创建的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现所需转换规则： 例如，你指定了未使用的本地 AD 属性（例如 extensionAttribute1）作为 UserType 的源属性，可以实现直接属性流：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | extensionAttribute1 | 未选中 | 更新 |

    再例如，可以从其他属性派生 UserType 属性的值。 例如，如果用户的本地 AD UserPrincipalName 属性结尾是域部分“@partners.fabrikam123.org”，则应将这些用户全部同步为 Guest。 可实现表达式：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | 未选中 | 更新 |

7. 单击“添加”创建入站规则。

![创建入站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步骤 5：创建流到 Azure AD 的属性值的出站同步规则
出站同步规则允许要在 Azure AD 中从 Metaverse 流向 PreferredDataLocation 属性的属性值：

1. 转到“同步规则”编辑器。
2. 将搜索筛选器的“方向”设置为“出站”。
3. 单击“添加新规则”按钮。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | ----- | ------ | --- |
    | 名称​​ | *提供名称* | 例如“Out to AAD – User UserType” |
    | 说明 | *提供说明* ||
    | 连接的系统 | *选择 AAD 连接器* ||
    | 连接的系统对象类型 | 用户 ||
    | Metaverse 对象类型 | **Person** ||
    | 链接类型 | **Join** ||
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加**包含两个子句的单个范围筛选器组**：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 用户 |
    | cloudMastered | NOTEQUAL | True |

    范围筛选器确定要将此出站同步规则应用到哪些 Azure AD 对象。 在本示例中，我们将使用“Out to AD – User Identity”OOB 同步规则中的相同范围筛选器。 它可以防止将同步规则应用到未从本地 Active Directory 同步的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | UserType | 未选中 | 更新 |

7. 关闭“添加”创建出站规则。

![创建出站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-full-synchronization-cycle"></a>步骤 6：运行完全同步周期
一般情况下，完全同步周期是必需的，因为我们已添加到这两个 AD 的新属性和 Azure AD 连接器架构和引入的自定义同步规则。 建议在将其导出到 Azure AD 之前验证所做的更改。 可使用以下步骤手动运行完全同步周期所构成的步骤时验证更改。

1. 在**本地 AD 连接器**上运行**完全导入**步骤：

   1. 在 Synchronization Service Manager 中转到“操作”选项卡。
   2. 右键单击“本地 AD 连接器”，并选择“运行...”
   3. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   4. 等待操作完成。

    > [!NOTE]
    > 如果源属性已包含在导入属性列表中，则可以在本地 AD 连接器上跳过“完全导入”。 换而言之，不需要在执行[步骤 2：将源属性添加到本地 AD 连接器架构](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)期间进行任何更改。

2. 在**Azure AD 连接器**上运行**完全导入**步骤：

   1. 右键单击“Azure AD 连接器”，并选择“运行...”
   2. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   3. 等待操作完成。

3. 验证现有 User 对象上的同步规则更改：

    源属性已从本地 Active Directory 和 Azure AD 中的 UserType 导入相应的连接器空间。 在继续执行完全同步步骤之前，建议在本地 AD 连接器空间中的现有用户对象上执行“预览”。 选择的对象应具有填充的源属性。 成功预览填充 Metaverse 中 UserType 是一个很好的指标，表示已配置同步规则正确。 有关如何执行**预览**的信息，请参阅[验证更改](#verify-the-change)部分。

4. 在**本地 AD 连接器**上运行**完全同步**步骤：

   1. 右键单击“本地 AD 连接器”，并选择“运行...”
   2. 在弹出对话框中，选择“完全同步”，并单击“确定”。
   3. 等待操作完成。

5. 验证 Azure AD 的**挂起的导出**：

   1. 右键单击“Azure AD 连接器”，并选择“搜索连接器空间”。

   2. 在“搜索连接器空间”弹出对话框中：

      1. 将“范围”设置为“挂起的导出”。
      2. 选中所有三个复选框，包括“添加”、“修改”和“删除”。
      3. 单击“搜索”按钮获取要导出其更改的对象列表。 若要检查给定对象的更改，请双击该对象。
      4. 验证更改是否符合需要。

6. 在**Azure AD 连接器**上运行**导出**步骤

   1. 右键单击“Azure AD 连接器”，并选择“运行...”
   2. 在“运行连接器”弹出对话框中选择“导出”，并单击“确定”。
   3. 等待导出到 Azure AD 完成。

> [!NOTE]
> 可能注意到这些步骤不包括完全同步步骤和 Azure AD 连接器上的导出步骤。 由于属性值只会从本地 Active Directory 流向 Azure AD，因此不需要执行这些步骤。

### <a name="step-7-re-enable-sync-scheduler"></a>步骤 7：重新启用同步计划程序
重新启用内置的同步计划程序：

1. 启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来重新启用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $true`


## <a name="next-steps"></a>后续步骤
* 在 [Understanding Declarative Provisioning](active-directory-aadconnectsync-understanding-declarative-provisioning.md)（了解声明性预配）中了解有关配置模型的详细信息。
* 在 [Understanding Declarative Provisioning Expressions](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)（了解声明性预配表达式）中了解有关表达式语言的详细信息。

**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
