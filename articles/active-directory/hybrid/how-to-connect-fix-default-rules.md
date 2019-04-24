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
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60352792"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>在 Azure AD Connect 中修复已修改的默认规则

Azure AD Connect 随附了有关同步的默认规则。  遗憾的是，这些规则并非普遍适用于所有组织，有时，你可能需要根据要求修改某些规则。

 如果你修改了默认规则或打算进行修改，请花片刻时间阅读本文档。

本文档将提供 2 个示例用于演示用户执行的最常见自定义操作，并说明如何正确实现这些自定义。

>[!NOTE] 
> 不支持通过修改现有默认规则来实现所需的自定义 - 这会导致无法将这些规则更新到将来发行版中的最新规则版本， 并且无法获取所需的 bug 修复和新功能。  本文档将介绍在不修改现有默认规则的情况下，如何实现相同的结果。 

## <a name="how-to-identify-modified-default-rules"></a>如何识别已修改的默认规则？
从 **Azure AD Connect** 版本 1.3.7.0 开始，可能很容易地识别已修改的默认规则。 可以转到桌面上的“应用”，并单击“同步规则编辑器”。

![编辑器](media/how-to-connect-fix-default-rules/default1.png)

在编辑器中，任何已修改默认规则的名称前面会显示一个图标，如下所示：

![icon](media/how-to-connect-fix-default-rules/default2.png)

 此外，该规则的旁边会显示一个同名的已禁用规则，它是标准的默认规则：

![默认规则](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常见自定义操作
下面是对默认规则执行的常见自定义操作：

- [更改属性流](#changing-attribute-flow)
- [更改范围筛选器](#changing-scoping-filter)
- [更改联接条件](#changing-join-condition)

## <a name="before-changing-any-rules"></a>更改任何规则之前
- 禁用同步计划程序。  默认情况下，计划程序每隔 30 分钟运行一次。 进行更改以及排查新规则错误时，需要确保其未启动。 要临时禁用计划程序，请启动 PowerShell，并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![默认规则](media/how-to-connect-fix-default-rules/default3.png)

- 更改范围筛选器可能会导致删除目标目录中的对象。 在对对象范围进行任何更改之前请保持谨慎。 建议先对暂存服务器进行更改，然后再对活动服务器进行更改。
- 添加任何新规则之后，请根据[验证同步规则](#validate-sync-rule)部分所述，对单个对象运行预览。
- 添加新规则或修改任何自定义同步规则之后，请运行完全同步。 这种同步会将新规则应用到所有对象。

## <a name="changing-attribute-flow"></a>更改属性流
属性流有 3 种不同的方案。让我们了解在不更改标准默认规则的情况下如何实现这些方案。
- 添加新属性
- 替代现有属性的值
- 不同步现有属性

### <a name="adding-new-attribute"></a>添加新属性：
如果你发现某个属性不会从源目录流向目标目录，可以使用 [Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)来流送新属性。

请注意，首要做法应该是使用 [Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)，这是 Azure AD Connect 提供的现成功能。 但是，如果这种做法不起作用，请执行以下步骤，以便在不修改现有标准默认同步规则的情况下流送属性。为此，可以添加两个新的同步规则。


#### <a name="add-an-inbound-sync-rule"></a>添加入站同步规则：
在入站同步规则中，属性的源是连接器空间，目标是 Metaverse。 例如，若要将本地 Active Directory 中的某个新属性流送到 Azure Active Directory，请创建一个新的入站同步规则，方法是启动“同步规则编辑器”，选择“入站”作为方向，然后单击“添加新规则”。 

 ![默认规则](media/how-to-connect-fix-default-rules/default3a.png)

根据自己的命名约定为规则命名，此处我们使用了 **Custom In from AD - User**，表示该规则是自定义规则，并且是从 AD 连接器空间到 Metaverse 的入站规则。   

 ![默认规则](media/how-to-connect-fix-default-rules/default3b.png)

为提供您自己的规则的说明，以便将来维护的规则的过程很简单，此规则的目标是什么和为什么需要它的等。
选择一个连接系统 （林） 的属性源。 然后，选择连接的系统对象类型和 Metaverse 对象类型。

指定优先级介于 0 到 99 （减少数量，较高优先级）。 将其他字段（例如“标记”、“启用密码同步”和“已禁用”）保留默认值。

将“范围筛选器”保留为空，表示该规则将应用到在 AD 联网系统与 Metaverse 之间联接的所有对象。

将“联接规则”保留为空，表示此规则将以标准默认规则中定义的联接条件为依据。 这是不禁用/删除标准默认规则的另一个原因，因为如果没有可依据的联接条件，则属性不会流动。 

为属性添加适当的转换。可以分配常量用于将常量值流送到目标属性、在源或目标属性之间进行定向映射，或者对属性使用表达式。 下面是可以使用的各种[表达式函数](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)。

#### <a name="add-an-outbound-sync-rule"></a>添加出站同步规则：
到目前为止，我们只是添加了一个入站同步规则，而没有将属性链接到目标目录，因此工作只完成了一半。 若要将属性链接到目标目录，需要创建一个出站规则，其中的源是 Metaverse，目标是联网系统。 若要创建出站规则，请启动“同步规则编辑器”，将“方向”更改为“出站”，然后单击“添加新规则”。 

![默认规则](media/how-to-connect-fix-default-rules/default3c.png)

与在入站规则中一样，可以使用自己的命名约定来为规则**命名**。 选择“Azure AD 租户”作为**联网系统**，并选择要设置其属性值的联网系统对象。 设置 0 - 99 的优先顺序。 

![默认规则](media/how-to-connect-fix-default-rules/default3d.png)

将“范围筛选器”保留为空，将“联接规则”保留为空，填写“常量”、“定向”或表达式作为转换。 

本示例演示了如何将 Active Directory 中某个用户对象的新属性流送到 Azure Active Directory。 可以使用这些步骤将任意对象中的任意属性映射到源和目标。  有关详细信息，请参阅[创建自定义同步规则](how-to-connect-create-custom-sync-rule.md)和[准备预配用户](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="overriding-value-of-existing-attribute"></a>替代现有属性的值
你可能想要替代已映射属性的值，例如，你始终想要对 Azure AD 中的某个属性设置 Null 值，为此，只需根据前面的步骤所述创建一个入站规则，并为目标属性流送 **AuthoritativeNull** 常量值。 请注意，在本例中，我们使用了 AuthoritativeNulll 而不是 Null。 这是因为，非 Null 值将取代 Null 值，即使前者的优先顺序更低（在规则中的优先顺序数字值更大）。 但是，AuthoritativeNull 将被视为 Null，而不会由其他规则中的非 Null 值取代。 

### <a name="dont-sync-existing-attribute"></a>不同步现有属性
若要从同步中排除某个属性，可以使用 Azure AD Connect 中提供的属性筛选功能。 从桌面图标启动“Azure AD Connect”，然后选择“自定义同步选项”。

![默认规则](media/how-to-connect-fix-default-rules/default4.png)

 确保“Azure AD 应用和属性筛选”已选中，然后单击“下一步”。

![默认规则](media/how-to-connect-fix-default-rules/default5.png)

取消选中要从同步中排除的属性。

![默认规则](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>更改范围筛选器
Azure AD Sync 会处理大部分对象，你可以缩小对象的范围，并以支持的方式减少要导出的对象数目，而无需更改标准默认同步规则。 若要增大对象的范围，可以**编辑**现有规则，克隆它，然后禁用原始规则。 Microsoft 建议不要增大 Azure AD Connect 所配置的范围。 增大对象范围会使得支持团队难以了解自定义操作以及为产品提供支持。

可通过以下方式缩小同步到 Azure AD 的对象的范围。 请注意，如果缩小所要同步的 **users** 的范围，则针对筛选出的用户执行的密码哈希同步也会停止。 如果对象已同步，则缩小范围后，将从目标目录中删除已筛选出的对象，因此，请慎重处理范围。
可通过以下支持的方法来缩小所要同步的对象的范围。

- [cloudFiltered 属性](#cloudfiltered-attribute)
- [OU 筛选](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
请注意，此属性不可以在 Active Directory 中设置。 需要根据“替代现有属性的值”部分所述添加新的入站规则，来设置此属性的值。 然后，可以使用**转换**和**表达式**在 Metaverse 中设置此属性。 在以下示例场景中，你不想要同步其部门名称以 **HRD**（不区分大小写）开头的所有用户：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我们已事先将源 (Active Directory) 中的部门名称转换为小写。 然后使用 Left 函数。我们只提取了前 3 个字符，并将其与 hrd 进行比较。 如果匹配，则将值设置为 True；否则设置为 NULL。 请注意，我们将设置 NULL 值，以便优先顺序更低（优先顺序数字值更大）的其他一些规则可以根据不同的条件写入该属性。 请针对一个对象运行预览，以根据[验证同步规则](#validate-sync-rule)部分所述验证同步规则。

![默认规则](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU 筛选
可以创建一个或多个 OU，并将你不想要同步的对象移到这些 OU。 然后在 Azure AD Connect 中配置 OU 筛选，方法是从桌面图标启动“Azure AD Connect”，并选择如下所示的选项。 也可以在安装 Azure AD Connect 时配置 OU 筛选。 

![默认规则](media/how-to-connect-fix-default-rules/default8.png)

遵循向导操作，并取消选择不想要同步的 OU。

![默认规则](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>更改联接条件
Microsoft 建议使用 Azure AD Connect 配置的默认联接条件。 更改默认联接条件会使得支持团队难以了解自定义操作以及为产品提供支持。

## <a name="validate-sync-rule"></a>验证同步规则
可以在不运行整个同步周期的情况下，使用预览功能来验证新添加的同步规则。 启动“同步服务”的 UI。

![默认规则](media/how-to-connect-fix-default-rules/default10.png)

单击“Metaverse 搜索”，选择“person”作为范围对象，**添加子句**并指定搜索条件。 单击“搜索”按钮并在“搜索结果”中双击该对象。请注意，在运行此步骤之前，应该先对林运行导入和同步，以确保 Azure AD Connect 中的数据对于该对象而言是最新的。

![默认规则](media/how-to-connect-fix-default-rules/default11.png)



选择“连接器”，在相应的连接器（林）中选择该对象，然后单击“属性...”。

![默认规则](media/how-to-connect-fix-default-rules/default12.png)

单击“预览...”

![默认规则](media/how-to-connect-fix-default-rules/default13a.png)

在左窗格中单击“生成预览”和“导入属性流”。

![默认规则](media/how-to-connect-fix-default-rules/default14.png)
 
在此处可以看到，新添加的规则已针对该对象运行，并且已将 cloudFiltered 属性设置为 True。

![默认规则](media/how-to-connect-fix-default-rules/default15a.png)
 
如何将已修改的规则与默认规则进行比较？
可将这两种规则单独作为文本文件导出。 这些规则将作为 PowerShell 脚本文件导出。 可以使用任何文件比较工具对其进行比较，以查看做出了哪种类型的更改。 以下示例使用了 windiff 来比较两个文件。
 
可以看到，在用户修改的规则中，msExchMailboxGuid 属性已更改为 **Expression** 类型而不是 **Direct**，并且使用了 **NULL** 值和 **ExecuteOnce** 选项。 可以忽略 Identified（已识别）和 Precedence（优先顺序）的差异。 

![默认规则](media/how-to-connect-fix-default-rules/default17.png)
 
如何修复已修改的默认规则？
若要将规则修复为默认设置，可按如下所示删除已修改的规则并启用默认规则，然后运行**完全同步**。 在这样做之前，请根据前面所述采取纠正措施，以免丢失你想要实现的自定义## 后续步骤

## <a name="next-steps"></a>后续步骤
- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)

