---
title: 如何修复修改后的默认规则-Azure AD Connect |Microsoft Docs
description: 了解如何修复 Azure AD Connect 附带的已修改默认规则。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036976"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>修复 Azure AD Connect 中修改的默认规则

Azure Active Directory （Azure AD） Connect 使用默认的同步规则。  遗憾的是，这些规则不适用于所有组织。 根据你的要求，你可能需要修改它们。 本文介绍了两个最常见的自定义的示例，并说明了实现这些自定义的正确方法。

>[!NOTE] 
> 不支持修改现有默认规则以实现所需的自定义。 如果这样做，则在将来的版本中，它会阻止将这些规则更新到最新版本。 您不会获得所需的 bug 修复程序或新功能。 本文档介绍了如何实现相同的结果，而无需修改现有的默认规则。 

## <a name="how-to-identify-modified-default-rules"></a>如何标识修改的默认规则
从 Azure AD Connect 的版本1.3.7.0 开始，可以轻松地识别修改后的默认规则。 请**在桌面上**选择 "应用"，并选择 "**同步规则编辑器**"。

![Azure AD Connect，其中突出显示了 "同步规则编辑器"](media/how-to-connect-fix-default-rules/default1.png)

在编辑器中，任何修改的默认规则都在名称前面显示一个警告图标。

![警告图标](media/how-to-connect-fix-default-rules/default2.png)

 此时将显示一个具有相同名称的禁用规则（这是标准的默认规则）。

!["同步规则编辑器"，显示标准默认规则和修改的默认规则](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常见自定义
以下是默认规则的常见自定义：

- 更改属性流
- 更改范围筛选器
- 更改联接条件

更改任何规则之前：

- 禁用同步计划程序。 默认情况下，计划程序每30分钟运行一次。 请确保在进行更改和排查新规则时，它不会启动。 若要临时禁用计划程序，请启动 PowerShell，并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![PowerShell 命令禁用同步计划程序](media/how-to-connect-fix-default-rules/default3.png)

- 范围筛选器中的更改可能会导致在目标目录中删除对象。 在对对象的作用域进行任何更改之前，请务必小心。 建议在活动服务器上进行更改之前，对过渡服务器进行更改。
- 在添加任何新规则后，在单个对象上运行预览，如 "[验证同步规则](#validate-sync-rule)" 一节中所述。
- 添加新规则或修改任何自定义同步规则后，请运行完全同步。 此同步将向所有对象应用新规则。

## <a name="change-attribute-flow"></a>更改属性流
有三种不同的方案可用于更改属性流：
- 添加新属性。
- 重写现有属性的值。
- 选择不同步现有属性。

你可以执行这些操作，而无需更改标准默认规则。

### <a name="add-a-new-attribute"></a>添加新属性
如果发现某个属性未从源目录流向目标目录，请使用[Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)来解决此问题。

如果扩展不起作用，请尝试添加两个新的同步规则，如以下部分中所述。


#### <a name="add-an-inbound-sync-rule"></a>添加入站同步规则
入站同步规则表示属性的源是连接器空间，目标是元节。 例如，若要使新的属性从本地 Active Directory 到 Azure Active Directory，请创建新的入站同步规则。 启动 "**同步规则编辑器**"，选择 "**入站**" 作为方向，然后选择 "**添加新规则**"。 

 ![同步规则编辑器](media/how-to-connect-fix-default-rules/default3a.png)

遵循您自己的命名约定来命名规则。 在这里，我们使用**来自 AD 用户的中的自定义**。 这意味着规则是自定义规则，并且是从 Active Directory 连接器空间到元节的入站规则。   

 ![创建入站同步规则](media/how-to-connect-fix-default-rules/default3b.png)

提供您自己的规则说明，以便将来维护规则。 例如，说明可以基于规则的目标以及需要它的原因。

对**连接的系统**、**连接的系统对象类型**和**元节对象类型**字段进行选择。

指定从0到99的优先级值（数字越小，优先级越高）。 对于**标记**，请**启用 "密码同步**" 和 "**已禁用**" 字段，并使用默认选项。

将**范围筛选器**留空。 这意味着规则适用于在 Active Directory 连接的系统和元节之间联接的所有对象。

将**联接规则**留空。 这意味着该规则使用在标准默认规则中定义的联接条件。 这是另一个原因是不禁用或删除标准默认规则。 如果没有联接条件，则该属性将不会流动。 

为属性添加适当的转换。 可以分配一个常量，使常量值流向目标属性。 可以在源属性或目标属性之间使用直接映射。 或者，可以将表达式用于特性。 下面是可以使用的各种[表达式函数](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)。

#### <a name="add-an-outbound-sync-rule"></a>添加出站同步规则
若要将属性链接到目标目录，需要创建出站规则。 这意味着源是元节，目标是连接的系统。 若要创建出站规则，请启动 "**同步规则编辑器**"，将**方向**更改为 "**出站**"，然后选择 "**添加新规则**"。 

![同步规则编辑器](media/how-to-connect-fix-default-rules/default3c.png)

与入站规则一样，你可以使用自己的命名约定来命名规则。 选择 "**连接的系统**" 作为 Azure AD 租户，并选择要将属性值设置为的连接的系统对象。 设置从0到99的优先级。 

![创建出站同步规则](media/how-to-connect-fix-default-rules/default3d.png)

将**范围筛选器**和**联接规则**留空。 填写转换为常量、直接或表达式。 

你现在知道如何将用户对象流的新属性从 Active Directory 到 Azure Active Directory。 您可以使用这些步骤将任何对象的任何属性映射到源和目标。 有关详细信息，请参阅[创建自定义同步规则](how-to-connect-create-custom-sync-rule.md)和[准备预配用户](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>重写现有属性的值
您可能想要重写已映射的属性的值。 例如，如果你始终需要将 null 值设置为 Azure AD 中的属性，只需创建入站规则。 将常数值 `AuthoritativeNull`传递到目标属性。 

>[!NOTE] 
> 在这种情况下，请使用 `AuthoritativeNull` 而不是 `Null`。 这是因为非 null 值将替换 null 值，即使它的优先级较低（规则中的数字值越大）。 另一方面，`AuthoritativeNull`不会被其他规则替换为非 null 值。 

### <a name="dont-sync-existing-attribute"></a>不同步现有属性
如果要从同步中排除某个属性，请使用 Azure AD Connect 中提供的属性筛选功能。 从桌面图标启动**Azure AD Connect** ，然后选择 "**自定义同步选项**"。

![Azure AD Connect 其他任务选项](media/how-to-connect-fix-default-rules/default4.png)

 请确保选择**Azure AD 应用和属性筛选**，然后选择 "**下一步**"。

![Azure AD Connect 可选功能](media/how-to-connect-fix-default-rules/default5.png)

清除要从同步中排除的属性。

![Azure AD Connect 特性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>更改范围筛选器
Azure AD Sync 负责处理大多数对象。 您可以减少对象的作用域，减少要导出的对象数，而无需更改标准的默认同步规则。 

使用以下方法之一来减少要同步的对象的范围：

- cloudFiltered 特性
- 组织单位筛选

如果减少了要同步的用户的作用域，则已筛选出的用户的密码哈希同步也会停止。 如果对象已在同步，则在减小作用域后，将从目标目录中删除筛选掉的对象。 出于此原因，请务必仔细确定范围。

>[!IMPORTANT] 
> 不建议增加 Azure AD Connect 配置的对象的作用域。 这样做会使 Microsoft 支持团队难以理解自定义。 如果必须增加对象的作用域，请编辑现有规则，将其克隆，并禁用原始规则。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 特性
无法在 Active Directory 中设置此属性。 通过添加新的入站规则，设置此属性的值。 然后，可以使用 "**转换**" 和 "**表达式**" 在元节中设置此属性。 以下示例显示你不希望同步其部门名称以**HRD**开头的所有用户（不区分大小写）：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

首先，我们将部门从源（Active Directory）转换为小写。 然后，使用 `Left` 函数，只使用前三个字符，并将其与 `hrd`进行比较。 如果匹配，则将该值设置为 `True`，否则 `NULL`。 在将值设置为 null 时，某些优先级较低的其他规则（较高的数值）可以使用不同的条件对其进行写入。 对一个对象运行预览版以验证同步规则，如 "[验证同步规则](#validate-sync-rule)" 一节中所述。

![创建入站同步规则选项](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>组织单位筛选
你可以创建一个或多个组织单位（Ou），并将不想同步的对象移到这些 Ou。 然后，在 Azure AD Connect 中配置 OU 筛选。 从桌面图标启动**Azure AD Connect** ，并选择以下选项。 你还可以在安装 Azure AD Connect 时配置 OU 筛选。 

![Azure AD Connect 其他任务](media/how-to-connect-fix-default-rules/default8.png)

按照向导操作，清除不想同步的 Ou。

![Azure AD Connect 域和 OU 筛选选项](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>更改联接条件
使用 Azure AD Connect 配置的默认联接条件。 更改默认的联接条件会使 Microsoft 支持人员难以了解自定义和产品支持。

## <a name="validate-sync-rule"></a>验证同步规则
您可以通过使用预览功能来验证新添加的同步规则，而无需运行完全同步周期。 在 Azure AD Connect 中，选择 "**同步服务**"。

![Azure AD Connect，其中突出显示了同步服务](media/how-to-connect-fix-default-rules/default10.png)

选择 "**元节搜索**"。 选择 "作用域对象" 作为**person**，选择 "**添加子句**"，并提及搜索条件。 接下来，选择 "**搜索**"，然后在搜索结果中双击该对象。 在运行此步骤之前，请通过在林中运行导入和同步，确保 Azure AD Connect 中的数据是最新的。

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

在 "**元节对象属性**" 中，选择 "**连接器**"，选择相应的连接器（林）中的对象，然后选择 "**属性 ...** "。

![Metaverse 对象属性](media/how-to-connect-fix-default-rules/default12.png)

选择**预览 ...**

![连接器空间对象属性](media/how-to-connect-fix-default-rules/default13a.png)

在预览窗口中，选择左窗格中的 "**生成预览**并**导入属性流**"。

![预览](media/how-to-connect-fix-default-rules/default14.png)
 
请注意，新添加的规则在对象上运行，并将 `cloudFiltered` 特性设置为 true。

![预览](media/how-to-connect-fix-default-rules/default15a.png)
 
若要将修改后的规则与默认规则进行比较，请将这两个规则作为文本文件单独导出。 这些规则作为 PowerShell 脚本文件导出。 可以通过使用任何文件比较工具（例如，windiff）对这些更改进行比较。 
 
请注意，在修改后的规则中，`msExchMailboxGuid` 属性改为**Expression**类型，而不是**直接**。 此外，该值将更改为**NULL**和**ExecuteOnce**选项。 您可以忽略标识的和优先差异。 

![windiff 工具输出](media/how-to-connect-fix-default-rules/default17.png)
 
若要修复规则以将其更改回默认设置，请删除修改后的规则并启用默认规则。 确保你不会丢失你要尝试实现的自定义。 准备就绪后，运行**完全同步**。

## <a name="next-steps"></a>后续步骤
- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)



