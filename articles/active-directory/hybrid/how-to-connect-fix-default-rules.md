---
title: 如何修复已修改的默认规则 - Azure AD Connect | Microsoft Docs
description: 了解如何修复 Azure AD Connect 随附的经过修改的默认规则。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320041"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>在 Azure AD Connect 中修复已修改的默认规则

Azure Active Directory (Azure AD) Connect 使用默认规则进行同步。  遗憾的是，这些规则并非普遍适用于所有组织。 你可能需要根据要求修改某些规则。 本文将通过两个示例介绍最常见的自定义操作，并说明如何正确实现这些自定义。

>[!NOTE] 
> 不支持通过修改现有默认规则来实现所需的自定义。 如果这样做，会导致无法将这些规则更新到将来发行版中的最新版本。 无法获取所需的 bug 修复或新功能。 本文档将介绍在不修改现有默认规则的情况下，如何实现相同的结果。 

## <a name="how-to-identify-modified-default-rules"></a>如何识别已修改的默认规则
从 Azure AD Connect 版本 1.3.7.0 开始，可能轻松识别已修改的默认规则。 转到“桌面上的应用”，然后选择“同步规则编辑器”即可。  

![Azure AD Connect，其中突出显示了“同步规则编辑器”](media/how-to-connect-fix-default-rules/default1.png)

在编辑器中，任何已修改的默认规则的名称前面会显示一个警告图标。

![警告图标](media/how-to-connect-fix-default-rules/default2.png)

 此外，该规则的旁边会显示一个同名的已禁用规则（标准的默认规则）。

![同步规则编辑器，其中显示了标准的默认规则和已修改的默认规则](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常见自定义操作
下面是对默认规则执行的常见自定义操作：

- 更改属性流
- 更改范围筛选器
- 更改联接条件

更改任何规则之前：

- 禁用同步计划程序。 默认情况下，计划程序每隔 30 分钟运行一次。 进行更改以及排查新规则错误时，需要确保其未启动。 若要暂时禁用计划程序，请启动 PowerShell 并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![用于禁用同步计划程序的 PowerShell 命令](media/how-to-connect-fix-default-rules/default3.png)

- 更改范围筛选器可能会导致删除目标目录中的对象。 在对对象范围进行任何更改之前请保持谨慎。 我们建议先对暂存服务器进行更改，然后再对活动服务器进行更改。
- 添加任何新规则之后，请根据[验证同步规则](#validate-sync-rule)部分所述，对单个对象运行预览。
- 添加新规则或修改任何自定义同步规则之后，请运行完全同步。 这种同步会将新规则应用到所有对象。

## <a name="change-attribute-flow"></a>更改属性流
可通过三种不同的方案来更改属性流：
- 添加新属性。
- 替代现有属性的值。
- 不同步现有属性。

可以在不更改标准默认规则的情况下执行这些操作。

### <a name="add-a-new-attribute"></a>添加新属性
如果你发现某个属性不会从源目录流向目标目录，请使用 [Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)来解决此问题。

如果不适合使用扩展，请尝试按照以下部分所述添加两个新的同步规则。


#### <a name="add-an-inbound-sync-rule"></a>添加入站同步规则
入站同步规则表示属性的源是连接器空间，目标是 Metaverse。 例如，若要将本地 Active Directory 中的某个新属性流送到 Azure Active Directory，请创建一个新的入站同步规则。 启动“同步规则编辑器”，选择“入站”作为方向，然后选择“添加新规则”。    

 ![屏幕截图，显示 "同步规则编辑器"，其中选择了 "入站" 和 "添加新规则"。](media/how-to-connect-fix-default-rules/default3a.png)

遵循自己的命名约定为规则命名。 此处我们使用了“Custom In from AD - User”。  这表示该规则是自定义规则，并且是从 Active Directory 连接器空间到 Metaverse 的入站规则。   

 ![创建入站同步规则](media/how-to-connect-fix-default-rules/default3b.png)

为规则提供自己的说明，以方便将来对其进行维护。 例如，可以根据此规则的目标以及为何需要它来提供说明。

在“连接的系统”、“连接的系统对象类型”和“Metaverse 对象类型”字段中做出选择。   

指定 0 到 99 的优先顺序值（数字越小，优先顺序越高）。 对于“标记”、“启用密码同步”和“已禁用”字段，请使用默认选项。   

将“范围筛选器”保留为空。  这表示该规则将应用到在 Active Directory 连接的系统与 Metaverse 之间联接的所有对象。

将“联接规则”保留为空。  这表示此规则将使用标准默认规则中定义的联接条件。 这是不禁用或删除标准默认规则的另一个原因。 如果没有联接条件，属性不会流动。 

为属性添加适当的转换。 可以分配一个常量用于将常量值流送到目标属性。 可以在源或目标属性之间使用直接映射。 或者，可对属性使用表达式。 下面是可以使用的各种[表达式函数](./reference-connect-sync-functions-reference.md)。

#### <a name="add-an-outbound-sync-rule"></a>添加出站同步规则
若要将属性链接到目标目录，需要创建出站规则。 这表示源是 Metaverse，目标是连接的系统。 若要创建出站规则，请启动“同步规则编辑器”，将“方向”更改为“出站”，然后选择“添加新规则”。     

![同步规则编辑器](media/how-to-connect-fix-default-rules/default3c.png)

与在入站规则中一样，可以使用自己的命名约定来为规则命名。 选择“Azure AD 租户”作为**连接的系统**，并选择要设置其属性值的联网系统对象。 设置从 0 到 99 的优先顺序。 

![创建出站同步规则](media/how-to-connect-fix-default-rules/default3d.png)

将“范围筛选器”和“联接规则”保留为空。   填写“常量”、“直接”或表达式作为转换。 

现在，你已了解如何将 Active Directory 中某个用户对象的新属性流送到 Azure Active Directory。 可以使用这些步骤将任意对象中的任意属性映射到源和目标。 有关详细信息，请参阅[创建自定义同步规则](how-to-connect-create-custom-sync-rule.md)和[准备预配用户](/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>替代现有属性的值
你可能想要替代已映射属性的值。 例如，你始终想要对 Azure AD 中的某个属性设置 null 值，为此，只需创建一个入站规则即可。 使常量值 `AuthoritativeNull` 流送到目标属性。 

>[!NOTE] 
> 在本例中，请使用 `AuthoritativeNull` 而不是 `Null`。 这是因为，非 Null 值将取代 Null 值，即使前者的优先顺序更低（在规则中的优先顺序数字值更大）。 另一方面，其他规则不会将 `AuthoritativeNull` 替换为非 null 值。 

### <a name="dont-sync-existing-attribute"></a>不同步现有属性
若要从同步中排除某个属性，请使用 Azure AD Connect 中提供的属性筛选功能。 从桌面图标启动“Azure AD Connect”，然后选择“自定义同步选项”。  

![Azure AD Connect 中的其他任务选项](media/how-to-connect-fix-default-rules/default4.png)

 确保“Azure AD 应用和属性筛选”已选中，然后选择“下一步”。  

![Azure AD Connect 可选功能](media/how-to-connect-fix-default-rules/default5.png)

清除要从同步中排除的属性。

![Azure AD Connect 属性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>更改范围筛选器
Azure AD Sync 负责处理大部分对象。 你可以缩小对象的范围，并减少要导出的对象数目，而无需更改标准默认同步规则。 

使用以下方法之一缩小所要同步的对象的范围：

- cloudFiltered 属性
- 组织单位筛选

如果缩小所要同步的用户范围，则针对筛选出的用户执行的密码哈希同步也会停止。 如果对象已在同步，则缩小范围后，将从目标目录中删除已筛选出的对象。 因此，请慎重使用范围。

>[!IMPORTANT] 
> 不建议增大 Azure AD Connect 配置的对象范围。 如果你这样做，Microsoft 支持团队将难以了解你的自定义操作。 如果必须增大对象的范围，请编辑现有规则，克隆它，然后禁用原始规则。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
无法在 Active Directory 中设置此属性。 需要通过添加新的入站规则来设置此属性的值。 可以使用“转换”和“表达式”在 Metaverse 中设置此属性。   在以下示例场景中，你不想要同步其部门名称以 **HRD**（不区分大小写）开头的所有用户：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我们已事先将源 (Active Directory) 中的部门名称转换为小写。 然后，我们只使用 `Left` 函数提取了前 3 个字符，并将其与 `hrd` 进行比较。 如果匹配，则将值设置为 `True`，否则设置为 `NULL`。 将值设置为 null 后，优先顺序较低（优先顺序数字值较大）的其他一些规则可以根据不同的条件写入该属性。 针对一个对象运行预览，以根据[验证同步规则](#validate-sync-rule)部分所述验证同步规则。

![创建入站同步规则的选项](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>组织单位筛选
可以创建一个或多个组织单位 (OU)，并将你不想要同步的对象移到这些 OU。 然后在 Azure AD Connect 中配置 OU 筛选。 从桌面图标启动“Azure AD Connect”，并选择以下选项。  也可以在安装 Azure AD Connect 时配置 OU 筛选。 

![Azure AD Connect 中的其他任务](media/how-to-connect-fix-default-rules/default8.png)

遵循向导操作，并清除不想要同步的 OU。

![Azure AD Connect 域和 OU 筛选选项](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>更改联接条件
使用 Azure AD Connect 配置的默认联接条件。 如果你更改默认联接条件，Microsoft 支持部门将难以了解自定义操作以及为产品提供支持。

## <a name="validate-sync-rule"></a>验证同步规则
可以在不运行整个同步周期的情况下，使用预览功能来验证新添加的同步规则。 在 Azure AD Connect 中选择“同步服务”。 

![Azure AD Connect，其中突出显示了“同步服务”](media/how-to-connect-fix-default-rules/default10.png)

选择“Metaverse 搜索”。  选择“person”作为范围对象，选择“添加子句”，并指定搜索条件。   接下来，选择“搜索”并在搜索结果中双击该对象。  在运行此步骤之前，请先对林运行导入和同步，以确保 Azure AD Connect 中的数据对于该对象而言是最新的。

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

在“Metaverse 对象属性”中选择“连接器”，在相应的连接器（林）中选择该对象，然后选择“属性...”。   

![Metaverse 对象属性](media/how-to-connect-fix-default-rules/default12.png)

选择“预览...” 

![连接器空间对象属性](media/how-to-connect-fix-default-rules/default13a.png)

在“预览”窗口的左窗格中选择“生成预览”和“导入属性流”。  

![显示 "预览" 窗口的屏幕截图，其中选择了 "导入属性流" 和 "生成预览"。](media/how-to-connect-fix-default-rules/default14.png)
 
在此处可以看到，新添加的规则已针对该对象运行，并且已将 `cloudFiltered` 属性设置为 true。

![预览](media/how-to-connect-fix-default-rules/default15a.png)
 
若要将已修改的规则与默认规则进行比较，请将这两种规则单独作为文本文件导出。 这些规则将作为 PowerShell 脚本文件导出。 可以使用任何文件比较工具（例如 windiff）对其进行比较，以查看所做的更改。 
 
可以看到，在修改的规则中，`msExchMailboxGuid` 属性已更改为 **Expression** 类型而不是 **Direct**。 此外，值已更改为 **NULL** 和 **ExecuteOnce** 选项。 可以忽略 Identified（已识别）和 Precedence（优先顺序）的差异。 

![windiff 工具输出](media/how-to-connect-fix-default-rules/default17.png)
 
若要修复规则以将其更改回默认设置，请删除已修改的规则并启用默认规则。 请确保不会丢失你正在尝试实现的自定义。 准备就绪后，运行**完全同步**。

## <a name="next-steps"></a>后续步骤
- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)