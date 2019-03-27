---
title: 如何解决 Azure AD Connect 的已修改的默认规则-|Microsoft Docs
description: 了解如何解决附带 Azure AD Connect 的已修改的默认规则。
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
ms.openlocfilehash: f524e8cef3878816cec53575217bdb6d0fd9be7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501251"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>在 Azure AD Connect 修复已修改的默认规则

Azure AD Connect 随附有同步的默认规则。  遗憾的是这些规则不执行普遍适用于所有组织，可能情况下，根据你的要求，当您需要对其进行修改时。

 如果你已修改的默认规则，或打算对其进行修改，请花片刻时间阅读本文档。

本文档提供的由用户完成的最常见的自定义的 2 个示例，并介绍了实现这些自定义的正确方法。

>[!NOTE] 
> 修改现有的默认规则，以实现所需的自定义项不支持-这样将阻止更新到最新版本在未来版本中的这些规则。 这将阻止你获取所需的 bug 修复和新功能。  本文档将介绍如何实现相同的结果，而无需修改现有的默认规则。 

## <a name="how-to-identify-modified-default-rules"></a>如何识别已修改的默认规则？
从版本 1.3.7.0 开始**Azure AD Connect**，现在很容易识别已修改的默认规则。 您可以在桌面上转到应用，单击**同步规则编辑器**。

![编辑器](media/how-to-connect-fix-default-rules/default1.png)

在编辑器中，任何已修改的默认规则在显示时名称前面的图标，如下所示：

![icon](media/how-to-connect-fix-default-rules/default2.png)

 此外将看到具有相同名称旁边的是标准的默认规则已禁用的规则：

![默认规则](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常见的自定义
以下是常见的自定义的默认规则：

- [更改属性流](#changing-attribute-flow)
- [更改范围筛选器](#changing-scoping-filter)
- [更改联接条件](#changing-join-condition)

## <a name="before-changing-any-rules"></a>然后再更改任何规则
- 禁用同步计划程序。  默认情况下，计划程序运行每隔 30 分钟。 进行更改以及排查新规则错误时，需要确保其未启动。 要临时禁用计划程序，请启动 PowerShell，并运行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![默认规则](media/how-to-connect-fix-default-rules/default3.png)

- 范围筛选器中的更改可能会导致删除目标目录中的对象。 请小心中对象的作用域进行任何更改之前。 建议在活动服务器上进行更改之前对临时服务器进行更改。
- 请在单个对象上运行预览中所述[验证同步规则](#validate-sync-rule)部分中，添加任何新的规则之后。
- 请添加新的规则或修改的任何自定义同步规则后运行完全同步。 此同步会将新规则应用于所有对象。

## <a name="changing-attribute-flow"></a>更改属性流
有 3 种不同方案的属性流，让我们看看如何实现此目的而无需更改标准默认规则。
- 添加新属性
- 重写现有属性的值
- 不同步现有的属性

### <a name="adding-new-attribute"></a>添加新属性：
如果您发现的目标目录中，将属性不流自源目录，则可以使用[Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)以传递新的属性。

请注意，第一个选择应使用[Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md)、 带 Azure AD Connect 提供的框功能。 但是，如果它不起作用，然后通过执行以下步骤来流送属性，而无需修改现有的标准默认同步规则，可以执行此操作通过添加两个新的同步规则。


#### <a name="add-an-inbound-sync-rule"></a>添加入站的同步规则：
入站的同步规则意味着该属性的源是连接器空间和目标是 metaverse。 例如，流从本地 Active Directory 到 Azure Active Directory 的新属性，创建新的入站的同步规则通过启动**同步规则编辑器**，然后选择方向作为**入站**然后单击**添加新规则**。 

 ![默认规则](media/how-to-connect-fix-default-rules/default3a.png)

请按照你自己的命名约定命名规则，此处我们使用**自定义 In from AD-用户**，也就是说，该规则是自定义规则，并且是从 AD 连接器空间到 Metaverse 的入站的规则。 

 ![默认规则](media/how-to-connect-fix-default-rules/default3b.png)

为提供您自己的规则的说明，以便将来维护的规则的过程很简单，此规则的目的是什么和为什么需要它的等。
选择已连接的系统 （林） 的属性的源。 然后连接的系统对象类型和 Metaverse 对象类型。

指定优先级介于 0 到 99 （减少数量，较高优先级）。 保留其他字段，例如标记，启用密码同步或已禁用为默认值。

保持范围筛选器为空，这意味着该规则将应用到已加入 AD 连接的系统和 Metaverse 之间的所有对象。

保持联接规则为空，这意味着此规则将通过标准的默认规则中定义的联接条件。 这是另一个原因不禁用/删除标准默认规则因为如果没有联接条件下面然后该属性不流动。 

添加相应的转换属性，可以为流到你的目标属性，常量值的常量或直接源或目标属性或属性的表达式之间的映射。 以下是各种[表达式函数](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)可以使用。

#### <a name="add-an-outbound-sync-rule"></a>添加出站同步规则：
到目前为止的只是一个入站的同步规则中添加我们投入了一半工作，因为该属性尚未链接到目标目录。 若要将该属性链接到目标总监，需要创建出站规则，这意味着源是 metaverse，目标是连接的系统。 若要创建出站规则，请启动**同步规则编辑器**，更改**方向**到**出站**然后单击**添加新规则**。 

![默认规则](media/how-to-connect-fix-default-rules/default3c.png)

如下所示的入站规则，可以使用你自己命名约定**名称**规则。 选择**连接的系统**作为 Azure AD 租户中，选择你想要设置的属性值的连接的系统对象。 设置优先级介于 0-99。 

![默认规则](media/how-to-connect-fix-default-rules/default3d.png)

保持**范围筛选器**为空，保持**联接规则**为空，填写常量、 直接或表达式的转换。 

在此示例中，我们已演示了如何流到 Azure Active Directory 从 Active Directory 用户对象的新属性。 可以使用以下步骤将映射到源和目标从任何对象中的任何属性。  有关详细信息请参阅[创建自定义同步规则](how-to-connect-create-custom-sync-rule.md)并[来设置用户准备](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="overriding-value-of-existing-attribute"></a>重写现有属性的值
可以你想要覆盖已映射的属性的值，例如始终希望在 Azure AD 中将 Null 值设置为一个属性，可以执行此操作通过在上一步和 flow 中所述，只需创建仅入站的规则**AuthoritativeNull**目标属性的常量值。 请注意，我们已使用 AuthoritativeNulll 而不是 Null 在这种情况下。 这是因为非 null 值将替换 Null 值，即使它具有较低优先级 （在规则中较高数字值）。 但是，AuthoritativeNull 将被视为 Null，并且将不替换为非 null 值的其他规则。 

### <a name="dont-sync-existing-attribute"></a>不同步现有的属性
如果你想要从同步中排除某个属性，您可以使用筛选功能在 Azure AD Connect 中提供的属性。 启动**Azure AD Connect**从桌面图标，然后选择**自定义同步选项**。

![默认规则](media/how-to-connect-fix-default-rules/default4.png)

 请确保**Azure AD 应用程序和属性筛选**处于选中状态，然后单击**下一步**。

![默认规则](media/how-to-connect-fix-default-rules/default5.png)

取消选中你想要从同步中排除的属性。

![默认规则](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>更改范围筛选器
Azure AD 同步处理的大多数对象，则可以减小对象的范围，并减少它更少的对象，而无需更改的标准默认同步规则导出中受支持的方式。 如果你想要增加对象的作用域，然后，你可以**编辑**现有规则，请将其克隆并禁用原始规则。 Microsoft 建议您不要增加作用域由 Azure AD Connect 配置。 增加作用域中的对象将使难以支持团队来了解自定义项并支持该产品。

下面是如何减少同步到 Azure AD 的对象的作用域。 请注意，如果你减少的作用域**用户**然后密码哈希同步也将停止为筛选出用户已同步。 如果对象已同步，然后缩小范围后, 筛选出对象将从目标目录中删除，请处理范围应非常小心。
下面是受支持的方法，以减少同步对象的作用域。

- [cloudFiltered 属性](#cloudfiltered-attribute)
- [OU 筛选](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
请注意，这不是可以在 Active Directory 中设置的属性。 您需要通过添加一个新的入站的规则，如中所述设置此属性的值**重写现有属性的值**部分。 然后，可以使用**转换**并用**表达式**在 Metaverse 中设置此属性。 下面是示例，您不想同步所有用户的部门名称开头不区分大小写**HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

首先，我们已为小写形式从源 (Active Directory) 转换该部门。 然后使用 Left 的函数，我们花费了仅前 3 个字符，并使用 hrd 比较。 如果匹配，然后将值设置为 True; 否则为 NULL。 请注意，我们将设置的值为 NULL，以便优先级较低 （更高版本的数字值） 的某些其他规则可以向其中写入具有不同的条件。 请在要验证同步规则中所述的一个对象上运行预览[验证同步规则](#validate-sync-rule)部分。

![默认规则](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU 筛选
您可以创建一个或多个 Ou 并移动不想要同步到这些 Ou 的对象。 然后配置通过启动在 Azure AD Connect 中进行筛选的 OU **Azure AD Connect**从桌面图标，选择的选项，如下所示。 此外可以配置在 Azure AD Connect 的安装时筛选的 OU。 

![默认规则](media/how-to-connect-fix-default-rules/default8.png)

按照向导操作，然后取消选择不想要同步的 Ou。

![默认规则](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>更改联接条件
Microsoft 建议你使用默认值加入 Azure AD connect 配置的条件。 更改默认的联接条件将使其难以支持团队来了解自定义项并支持该产品。

## <a name="validate-sync-rule"></a>验证同步规则
可以通过使用预览功能，而不运行完全同步周期验证新添加的同步规则。 启动**同步服务**UI。

![默认规则](media/how-to-connect-fix-default-rules/default10.png)

单击**Metaverse 搜索**，选择为作用域对象**人员**，**添加子句**并指明您的搜索条件。 单击**搜索**按钮，然后在对象上双击**搜索结果**请注意，你将林的运行导入和同步运行此步骤之前，这是为了确保 Azure AD Connect 中的数据为该对象的最新。

![默认规则](media/how-to-connect-fix-default-rules/default11.png)



选择**连接器**，在相应 connector(forest) 中选择的对象，单击**属性...**.

![默认规则](media/how-to-connect-fix-default-rules/default12.png)

单击**预览...**

![默认规则](media/how-to-connect-fix-default-rules/default13a.png)

单击**生成预览**并**导入属性流**的左窗格中。

![默认规则](media/how-to-connect-fix-default-rules/default14.png)
 
此处您会注意到新添加的规则在对象上运行，并且已将 cloudFiltered 属性设置为 True。

![默认规则](media/how-to-connect-fix-default-rules/default15a.png)
 
如何比较已修改的规则与默认规则？
您可以单独导出这两个规则，为文本文件。 这些规则将导出为 powershell 脚本文件。 您可以比较它们使用任何文件比较工具查看完成什么类型的更改。 此处在此示例中我使用 windiff 比较两个文件。
 
你可以注意到，在用户修改规则、 msExchMailboxGuid 属性更改为**表达式**类型而非**直接**值作为**NULL**和**ExecuteOnce**选项。 你可以忽略已确定和优先顺序的差异。 

![默认规则](media/how-to-connect-fix-default-rules/default17.png)
 
如何修复已修改的默认规则？
若要解决你的规则为默认设置，可以删除已修改的规则和启用的默认规则，如下所示，然后运行**完全同步**。 在这样做，请采取纠正措施，正如上面提到的以便不会丢失自定义项之前想要实现 # # 后续步骤

## <a name="next-steps"></a>后续步骤
- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)

