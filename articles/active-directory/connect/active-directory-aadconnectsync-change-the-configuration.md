---
title: "Azure AD Connect 同步：在 Azure AD Connect 同步中进行配置更改 | Microsoft 文档"
description: "介绍如何对 Azure AD Connect 同步中的配置进行更改。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2018
ms.author: billmath
ms.openlocfilehash: e97d3e3e35ee87864c5d38e75e08e62088e25fdb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect 同步：如何更改默认配置
本文旨在介绍如何对 Azure Active Directory (Azure AD) Connect 同步中的默认配置进行更改。其中提供了一些常见方案的步骤。 了解这些知识后，用户应该能够根据自己的业务规则对自己的配置进行简单的更改。

## <a name="synchronization-rules-editor"></a>同步规则编辑器
同步规则编辑器用于查看和更改默认配置。 可以在“Azure AD Connect”组下的“开始”菜单中找到它。  
![具有同步规则编辑器的开始菜单](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

打开编辑器时，将看到现成可用的默认规则。

![同步规则编辑器](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>在编辑器中导航
通过编辑器顶部的下拉列表可以快速查找特定规则。 例如，如果想要查看包含属性 proxyAddresses 的规则，可将下拉列表更改为：  
![SRE 筛选](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
若要重置筛选并加载新配置，请按键盘上的 F5。

右上角上有一个“添加新规则”按钮。 使用此按钮可以创建自己的自定义规则。

顶部有用于执行所选同步规则的按钮。 “编辑”和“删除”按钮可执行其应有的操作。 “导出”按钮可生成用于创建同步规则的 PowerShell 脚本。 使用此过程可将同步规则从一台服务器移到另一台。

## <a name="create-your-first-custom-rule"></a>创建第一个自定义规则
最常见的更改是对属性流的更改。 源目录中的数据可能与 Azure AD 中的不同。 在本部分的示例中，需要确保用户的给定名称始终为首字母大写。

### <a name="disable-the-scheduler"></a>禁用计划程序
默认情况下，[计划程序](active-directory-aadconnectsync-feature-scheduler.md)每 30 分钟运行一次。 进行更改以及排查新规则错误时，需要确保其未启动。 要临时禁用计划程序，请启动 PowerShell，并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。

![禁用计划程序](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>创建规则
1. 单击“添加新规则”。
2. 在“说明”页上输入以下内容：  
   ![入站规则筛选](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **名称**：为规则提供说明性名称。
   * **说明**：提供一些说明以便他人可以理解规则的用途。
   * **连接的系统**：可从中找到对象的系统。 在本例中，请选择“Active Directory 连接器”。
   * **连接的系统/Metaverse 对象类型**：分别选择“用户”和“人员”。
   * **链接类型**：将该值更改为“联接”。
   * **优先级**：提供系统中唯一的值。 较低的数值表示较高的优先级。
   * **标记**：将此项留空。 只有 Microsoft 中现成可用的规则应该会要求在此框中填入值。
3. 在“范围筛选器”页上，输入“givenName ISNOTNULL”。  
   ![入站规则范围筛选器](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   此部分用于定义规则应该应用到哪些对象。 如果留空，该规则会应用到所有用户对象。 但也可包括会议室、服务帐户和其他非个人用户对象。
4. 在“联接规则”页上，将字段留空。
5. 在“转换”页上，将 FlowType 更改为 **Expression**。 对于“目标属性”，请选择“givenName”。 对于“源”，请输入 **PCase([givenName])**。
   ![入站规则转换](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   函数名称和属性名称上的同步引擎要区分大小写。 如果键入出错，则添加规则时会看到警告。 可以保存并继续，但需要重新打开规则并进行更正。
6. 单击“添加”保存规则。

新的自定义规则应该与系统中的其他同步规则一起显示。

### <a name="verify-the-change"></a>验证更改
采用此新更改后，需要确保其按预期方式工作并且不会引发任何错误。 根据拥有的对象数量，有两种方法执行此步骤。

- 在所有对象上运行完全同步。
- 在单个对象上运行预览和完全同步。

从“开始”菜单打开“同步服务”。 本部分中的步骤全部在此工具中。

**针对所有对象的完全同步**  

   1. 选择顶部的“连接器”。 标识在前面部分中进行过更改的连接器（在本例中为 Active Directory 域服务），并选中它。 
   2. 对于“操作”，请选择“运行”。
   3. 依次选择“完全同步”、“确定”。
   ![完全同步](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   现已更新了 metaverse 中的对象。 查看 metaverse 中的对象来验证更改。

**在单个对象上的预览和完全同步**  

   1. 选择顶部的“连接器”。 标识在前面部分中进行过更改的连接器（在本例中为 Active Directory 域服务），并选中它。
   2. 选择“搜索连接器空间”。 
   3. 使用“范围”查找想要用于测试更改的对象。 选择该对象，并单击“预览”。 
   4. 在新的屏幕中，选择“提交预览”。  
   ![提交预览](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   现已将更改提交到 metaverse。

**查看 metaverse 中的对象**  

1. 选择几个示例对象，确保值符合预期并已应用规则。 
2. 从顶部选择“Metaverse 搜索”。 添加查找相关对象所需的筛选器。 
3. 从搜索结果中，打开对象。 查看属性值，同时还要在按预期方式应用规则的“同步规则”列中进行验证。  
![Metaverse 搜索](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>启用计划程序
如果一切按预期方式进行，可以再次启用计划程序。 从 PowerShell 中运行 `Set-ADSyncScheduler -SyncCycleEnabled $true`。

## <a name="other-common-attribute-flow-changes"></a>其他常见的属性流更改
前面部分介绍了如何更改属性流。 本部分提供了另外一些示例。 虽然创建同步规则的步骤已缩简，但可以在前面部分中找到完整步骤。

### <a name="use-an-attribute-other-than-the-default"></a>使用其他属性而不是默认属性
在此 Fabrikam 方案中，有对名字、姓氏和显示名称使用本地字母的林。 以拉丁字母表示的这些属性可在扩展属性中找到。 在 Azure AD 和 Office 365 中创建全局地址列表时，组织反而想要使用这些属性。

使用默认配置时，本地林中的对象如下所示：  
![属性流 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

若要使用其他属性流创建规则，请执行以下操作：

1. 从“开始”菜单打开“同步规则编辑器”。
2. 在左侧依然选定了“入站”的情况下，单击“添加新规则”按钮。
3. 为规则指定名称和说明。 选择本地 Active Directory 实例和相关的对象类型。 在“链接类型”中选择“联接”。 为“优先级”选择一个未被其他规则使用的数字。 现成的规则从 100 开始，因此该示例可以使用值 50。
  ![属性流 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. 将“范围筛选器”留空。 （即它应该应用到林中的所有用户对象。）
5. 将“联接规则”留空。 （即让现成的规则处理所有联接。）
6. 在“转换”中创建以下流：  
  ![属性流 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. 单击“添加”保存规则。
8. 转到“同步服务管理器”。 在“连接器”上，选择已在其中添加了规则的连接器。 依次选择“运行”、“完全同步”。 完全同步将使用当前规则重新计算所有对象。

这是使用此自定义规则的同一对象的结果：  
![属性流 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>属性的长度
字符串属性默认为可编制索引，并且最大长度为 448 个字符。 如果使用其中可能包含更多字符的字符串属性，请确保属性流中包括以下内容：  
`attributeName` <- `Left([attributeName],448)`：

### <a name="changing-the-userprincipalsuffix"></a>更改 userPrincipalSuffix
Active Directory 中的 userPrincipalName 属性并非始终被用户知晓，并且可能不适合作为登录 ID。 使用 Azure AD Connect 同步安装向导可以选择不同的属性 -- 例如 *mail*。 但在某些情况下，必须计算该属性。

例如：公司 Contoso 具有两个 Azure AD 目录，一个用于生产，另一个用于测试。 他们希望测试租户中的用户使用登录 ID 中的另一后缀：  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`：

在此表达式中，使用第一个 @-sign (Word) 的左侧所有内容，并与固定字符串连接。

### <a name="convert-a-multi-value-attribute-to-single-value"></a>将多值属性转换为单值属性
Active Directory 中的某些属性在架构中是多值，不过它们在 Active Directory 用户和计算机中看上去是单值。 一个示例就是说明属性：  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`：

在此表达式中，如果属性具有值，请使用属性中的第一项 (*Item*)，删除前导空格和尾随空格 (*Trim*)，并保留字符串中的前 448 个字符 (*Left*)。

### <a name="do-not-flow-an-attribute"></a>不要流送属性
有关本部分方案的背景信息，请参阅[控制属性流过程](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process)。

有两种方法可防止流送属性。 第一种方法是使用安装向导[删除选定的属性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。 如果以前未曾同步该属性，则可以使用这个选项。 但是，如果已开始同步此属性，后来使用此功能将它删除，则同步引擎将停止管理属性，现有值将保留在 Azure AD 中。

如果想要删除某个属性的值并确保将来不会流送该属性，则需要创建自定义规则。

在此 Fabrikam 方案中，我们在同步到云的属性中发现了一些不应该存在的属性。 我们希望确保从 Azure AD 中删除这些属性。  
![错误的扩展属性](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. 创建新的入站同步规则并填充说明。
  ![说明](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. 创建 **FlowType** 为 **Expression** 且 **Source** 为 **AuthoritativeNull** 的属性流。 即使优先顺序较低的同步规则尝试填充值，文本值 **AuthoritativeNull** 也会指出 metaverse 中的值应该为空。
  ![扩展属性的转换](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. 保存同步规则。 启动“同步服务”，查找“连接器”，并依次选择“运行”和“完全同步”。 此步骤将重新计算所有属性流。
4. 通过搜索连接器空间来验证是否即将导出所需的更改。
  ![分阶段删除](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>使用 PowerShell 创建规则
如只需进行少量更改，使用同步规则编辑器即可达到目的。 如需进行大量更改，最好选择 PowerShell。 某些高级功能只有 PowerShell 中才会提供。

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>获取现成规则的 PowerShell 脚本
要查看创建现成规则的 PowerShell 脚本，在同步规则编辑器中选择此规则，并单击“导出”。 此操作会提供创建该规则的 PowerShell 脚本。

### <a name="advanced-precedence"></a>高级优先级
以 100 优先级值开头的现成同步规则。 如果有多个林，并且需要进行大量自定义更改，那么 99 个同步规则可能不够。

可以指示同步引擎需要在现成规则前插入其他规则。 若要获取此行为，请执行以下步骤：

1. 在同步规则编辑器中标记第一个现成同步规则 (**In from AD-User Join**)，并选择“导出”。 复制 SR 标识符值。  
![更改前的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. 创建新的同步规则。 可使用同步规则编辑器创建它。 将规则导出到 PowerShell 脚本。
3. 在 **PrecedenceBefore** 属性中，从现成规则插入标识符值。 将“优先级”设置为“0”。 请确保标识符属性唯一，且不会从另一规则中重复使用某个 GUID。 此外，请确保未设置 **ImmutableTag** 属性。 仅为现成规则设置该属性。
4. 保存 PowerShell 脚本并运行它。 这样就会为自定义规则分配优先级值 100，且所有其他现成规则优先级值随之递增。  
![更改后的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

必要时，可让多个自定义同步规则使用相同的 **PrecedenceBefore** 值。

## <a name="enable-synchronization-of-usertype"></a>启用 UserType 同步
Azure AD Connect 支持 1.1.524.0 及更高版本中 **User** 对象的 **UserType** 属性同步。 更具体地讲，已引入以下更改：

- Azure AD 连接器中对象类型 **User** 的架构经过扩展，包含字符串类型的单值 UserType 属性。
- metaverse 中对象类型 **Person** 的架构经过扩展，包含字符串类型的单值 UserType 属性。

默认情况下，UserType 属性未启用同步，因为在本地 Active Directory 中没有相应的 UserType 属性。 必须手动启用同步。 执行此操作之前，必须注意 Azure AD 强制实施的以下行为：

- Azure AD 只接受 UserType 属性的两个值 – **Member** 和 **Guest**。
- 如果没有在 Azure AD Connect 中启用 UserType 属性同步，则通过目录同步创建的 Azure AD 用户的 UserType 属性将设置为 **Member**。
- Azure AD 不允许 Azure AD Connect 更改现有 Azure AD 用户的 UserType 属性。 该属性只能在 Azure AD 用户创建过程中设置。

在启用 UserType 属性同步之前，必须首先确定如何从本地 Active Directory 派生属性。 下面是最常见的方法：

- 指定要用作源属性的尚未使用的本地 AD 属性（例如 extensionAttribute1）。 指定的本地 AD 属性应为**字符串**类型，具有单值，且包含值 **Member** 或 **Guest**。 

    如果选择此方法，则在启用 UserType 属性同步之前，对于同步到 Azure AD 的本地 Active Directory 中的所有现有用户对象，必须确保指定的属性填充了正确的值。

- 或者，可以从其他属性派生 UserType 属性的值。 例如，如果用户的本地 AD userPrincipalName 属性结尾是域部分 *@partners.fabrikam123.org*，则应将这些用户全部同步为 **Guest**。 

    如前所述，Azure AD Connect 不允许 Azure AD Connect 更改现有 Azure AD 用户的 UserType 属性。 因此，对于租户中的所有 Azure AD 用户，必须确保采用的逻辑与 UserType 属性的现有配置方式一致。

启用 UserType 属性同步的步骤可归纳如下：

1.  禁用同步计划程序，并验证是否没有正在进行的同步操作。
2.  将源属性添加到本地 AD 连接器架构。
3.  将 UserType 添加到 Azure AD 连接器架构。
4.  创建流从本地 Active Directory 的属性值的入站的同步规则。
5.  创建流到 Azure AD 的属性值的出站同步规则。
6.  运行完全同步周期。
7.  启用同步计划程序。

>[!NOTE]
> 本节其余部分介绍了这些步骤。 在 Azure AD 部署使用单林拓扑和不使用自定义同步规则的上下文中描述它们。 如果有多林拓扑、自定义同步规则配置或者过渡服务器，则需要相应地调整步骤。

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步骤 1：禁用同步计划程序，并验证是否没有正在进行的同步操作。
若要避免将意外的更改导出到 Azure AD，请确保实现新同步规则的中途不会发生同步。 若要禁用内置的同步计划程序，请执行以下操作：

 1. 在 Azure AD Connect 服务器上启动 PowerShell 会话。
 2. 通过运行 cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false` 来禁用计划的同步。
 3. 转到“开始” > “同步服务”，打开 Synchronization Service Manager。
 4. 转到“操作”选项卡，确认是否不存在状态为“正在进行”的操作。

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>步骤 2：将源属性添加到本地 AD 连接器架构
并非所有 Azure AD 属性都将导入本地 AD 连接器空间。 要将源属性添加到导入属性的列表：

 1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
 2. 右键单击本地 AD 连接器，并选择“属性”。
 3. 在弹出对话框中，转到“选择属性”选项卡。
 4. 确保在属性列表中选中源属性。
 5. 单击“确定”保存。
![将源属性添加到本地 AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>步骤 3：将 UserType 添加到 Azure AD 连接器架构
默认情况下，UserType 属性不会导入 Azure AD 连接空间。 将 UserType 属性添加到导入属性的列表：

 1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
 2. 右键单击“Azure AD 连接器”，并选择“属性”。
 3. 在弹出对话框中，转到“选择属性”选项卡。
 4. 确保在属性列表中选中 PreferredDataLocation 属性。
 5. 单击“确定”保存。

![将源属性添加到 Azure AD 连接器架构](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步骤 4：创建流从本地 Active Directory 的属性值的入站的同步规则
入站同步规则允许要流到 metaverse 源属性从本地 Active Directory 中的属性值：

1. 转到“开始” > “同步规则编辑器”，打开同步规则编辑器。
2. 将搜索筛选器的“方向”设置为“入站”。
3. 单击“添加新规则”按钮创建新的入站规则。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | --- | --- | --- |
    | 名称 | *提供名称* | 例如 *In from AD – User UserType* |
    | 说明 | *提供说明* |  |
    | 连接的系统 | *选择本地 AD 连接器* |  |
    | 连接的系统对象类型 | **User** |  |
    | Metaverse 对象类型 | **Person** |  |
    | 链接类型 | **Join** |  |
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加包含以下子句的**单个范围筛选器组**：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | 用户\_ |

    范围筛选器确定要将此入站同步规则应用到哪些本地 AD 对象。 在本示例中，我们将使用 *In from AD – User Common* 现成同步规则中所用的相同范围筛选器，防止将同步规则应用到通过 Azure AD 用户写回功能创建的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现所需转换规则。 例如，如果指定了未使用的本地 AD 属性（例如 extensionAttribute1）作为 UserType 的源属性，则可以实现直接属性流：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | extensionAttribute1 | 未选中 | 更新 |

    另举一例，我们可以从其他属性派生 UserType 属性的值。 例如，如果用户的本地 AD userPrincipalName 属性结尾是域部分 *@partners.fabrikam123.org*，则应将这些用户全部同步为 Guest。可如下所示实现表达式：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | 未选中 | 更新 |

7. 单击“添加”创建入站规则。

![创建入站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步骤 5：创建流到 Azure AD 的属性值的出站同步规则
出站同步规则允许要在 Azure AD 中从 metaverse 流向 PreferredDataLocation 属性的属性值：

1. 转到“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“出站”。
3. 单击“添加新规则”按钮。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | ----- | ------ | --- |
    | 名称 | *提供名称* | 例如 *Out to AAD – User UserType* |
    | 说明 | *提供说明* ||
    | 连接的系统 | *选择 AAD 连接器* ||
    | 连接的系统对象类型 | **User** ||
    | Metaverse 对象类型 | **Person** ||
    | 链接类型 | **Join** ||
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加包含两个子句的**单个范围筛选器组**：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 用户 |
    | cloudMastered | NOTEQUAL | True |

    范围筛选器确定要将此出站同步规则应用到哪些 Azure AD 对象。 在本示例中，我们将使用 *Out to AD – User Identity* 现成同步规则中的相同范围筛选器。 它可以防止将同步规则应用到未从本地 Active Directory 同步的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | Source | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | UserType | 未选中 | 更新 |

7. 单击“添加”创建出站规则。

![创建出站同步规则](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>步骤 6：运行完全同步周期
一般情况下，完全同步周期是必需的，因为我们已添加到这两个 Active Directory 的新属性和 Azure AD 连接器架构和引入的自定义同步规则。 建议在将更改导出到 Azure AD 之前验证更改。 

可使用以下步骤手动运行完全同步周期所构成的步骤时验证更改。

1. 在**本地 AD 连接器**上运行**完全导入**：

   1. 在 Synchronization Service Manager 中转到“操作”选项卡。
   2. 右键单击“本地 AD 连接器”，并选择“运行”。
   3. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   4. 等待操作完成。

    > [!NOTE]
    > 如果源属性已包含在导入属性列表中，则可以在本地 AD 连接器上跳过“完全导入”。 换而言之，不需要在执行[步骤 2：将源属性添加到本地 AD 连接器架构](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)期间进行任何更改。

2. 在 **Azure AD 连接器**上运行“完全导入”：

   1. 右键单击“Azure AD 连接器”，并选择“运行”。
   2. 在弹出对话框中，选择“完全导入”，并单击“确定”。
   3. 等待操作完成。

3. 验证现有 User 对象上的同步规则更改：

    源属性已从本地 Active Directory 和 Azure AD 中的 UserType 导入相应的连接器空间。 在继续执行完全同步之前，请在本地 AD 连接器空间中的现有用户对象上执行“预览”。 选择的对象应具有填充的源属性。
    
    成功**预览**填充 metaverse 中 UserType 是一个很好的指标，表示已配置同步规则正确。 有关如何执行**预览**的信息，请参阅[验证更改](#verify-the-change)部分。

4. 在**本地 AD 连接器**上运行**完全同步**：

   1. 右键单击“本地 AD 连接器”，并选择“运行”。
   2. 在弹出对话框中，选择“完全同步”，并单击“确定”。
   3. 等待操作完成。

5. 验证 Azure AD 的**挂起的导出**：

   1. 右键单击“Azure AD连接器”，并选择“搜索连接器空间”。
   2. 在“搜索连接器空间”弹出对话框中：

      - 将“范围”设置为“挂起的导出”。
      - 选中所有三个复选框：“添加”、“修改”和“删除”。
      - 单击“搜索”按钮获取要导出其更改的对象列表。 若要检查给定对象的更改，请双击该对象。
      - 验证更改是否符合需要。

6. 在**Azure AD 连接器**上运行**导出**：

   1. 右键单击“Azure AD 连接器”，并选择“运行”。
   2. 在“运行连接器”弹出对话框中选择“导出”，并单击“确定”。
   3. 等待导出到 Azure AD 完成。

> [!NOTE]
> 这些步骤不包括完全同步步骤和 Azure AD 连接器上的导出步骤。 由于属性值只会从本地 Active Directory 流向 Azure AD，因此不需要执行这些步骤。

### <a name="step-7-re-enable-the-sync-scheduler"></a>步骤 7：重新启用同步计划程序
重新启用内置的同步计划程序：

1. 启动 PowerShell 会话。
2. 通过运行 cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true` 来重新启用计划的同步。


## <a name="next-steps"></a>后续步骤
* 在 [Understanding Declarative Provisioning](active-directory-aadconnectsync-understanding-declarative-provisioning.md)（了解声明性预配）中了解有关配置模型的详细信息。
* 在 [Understanding Declarative Provisioning Expressions](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)（了解声明性预配表达式）中了解有关表达式语言的详细信息。

**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
