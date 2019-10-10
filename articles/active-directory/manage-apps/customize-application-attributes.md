---
title: 自定义 Azure AD 属性映射 | Microsoft Docs
description: 了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足业务需求。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240996"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射

Microsoft Azure AD 为第三方 SaaS 应用程序（如 Salesforce、G Suite 等）提供对用户预配的支持。 如果为第三方 SaaS 应用程序启用用户预配，则 Azure 门户通过属性映射控制其属性值。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 某些应用管理其他类型的对象，以及用户（如组）。

可以根据业务需求自定义默认的属性映射。 因此，你可以更改或删除现有的属性映射，或者创建新的属性映射。

## <a name="editing-user-attribute-mappings"></a>编辑用户属性映射

请按照以下步骤访问用户预配的**映射**功能：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用程序以加载其应用管理窗格，您可以在其中查看报告和管理应用设置。
1. 选择 "**设置**" 以管理所选应用的用户帐户预配设置。
1. 展开 "**映射**"，以查看和编辑在 Azure AD 和目标应用程序之间流动的用户属性。 如果目标应用程序支持它，则可以选择配置组和用户帐户的预配。

   ![使用映射查看和编辑用户属性](./media/customize-application-attributes/21.png)

1. 选择**映射**配置以打开相关的**属性映射**屏幕。 要使 SaaS 应用程序正常运行，需要一些属性映射。 对于必需属性，“删除”功能将不可用。

   ![使用属性映射来配置应用的属性映射](./media/customize-application-attributes/22.png)

   在此屏幕截图中，可以看到，Salesforce 中托管对象的**Username**属性用链接的 Azure Active Directory 对象的**userPrincipalName**值填充。

1. 选择现有的**属性映射**，以打开 "**编辑属性**" 屏幕。 在此处可以编辑在 Azure AD 和目标应用程序之间流动的用户属性。

   ![使用编辑属性编辑用户属性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解属性映射类型

使用属性映射可以控制属性在第三方 SaaS 应用程序中的填充方式。
支持四种不同的映射类型：

- **直接** – 目标属性由 Azure AD 中的链接对象的属性值填充。
- **常量**-目标属性使用指定的特定字符串填充。
- **表达式** - 目标属性是基于脚本式表达式的结果填充的。
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](functions-for-customizing-application-data.md)。
- **无** - 目标属性保持不变。 但是，如果目标属性曾经为空，则会用您指定的默认值进行填充。

除了这四种基本类型外，自定义属性映射还支持可选**默认**值赋值的概念。 如果在 Azure AD 或目标对象上没有值，则默认值分配将确保用值填充目标属性。 最常见的配置是将此项留空。

### <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

在上一节中，已将你引入 "属性映射类型" 属性。
除了此属性，属性映射还支持以下属性：

- **源属性** - 来自源系统的用户属性（示例：Azure Active Directory）。
- **目标属性** - 目标系统中的用户属性（示例：ServiceNow）。
- **使用此属性匹配对象**–是否应使用此映射唯一标识源系统和目标系统之间的用户。 它通常在 Azure AD 中的 userPrincipalName 或 mail 属性上设置，该属性通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性。 如果有多个，则按此字段定义的顺序对它们进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。
- **应用此映射**
  - **始终**–在用户的创建和更新操作中应用此映射。
  - **仅在创建过程中**-仅在用户创建操作时应用此映射。

## <a name="editing-group-attribute-mappings"></a>编辑组属性映射

选定数量的应用程序（如 ServiceNow、Box 和 G Suite）支持预配组对象和用户对象。 组对象可以包含组属性（如显示名称和电子邮件别名）以及组成员。

![示例显示具有预配组和用户对象的 ServiceNow](./media/customize-application-attributes/24.png)

可以选择启用或禁用组设置，方法是在 "**映射**" 下选择组映射，并在 "**属性映射**" 屏幕中选择 "**启用**" 选项。

可以自定义预配为“组”对象的一部分的属性，就像前面所述的预配“用户”对象一样。 

> [!TIP]
> 预配组对象（属性和成员）的过程完全不同于[将组分配](assign-user-or-group-access-portal.md)到应用程序。 可以将组分配到应用程序，但只能预配组中包含的用户对象。 预配整个组对象不需要使用分配中的组。

## <a name="editing-the-list-of-supported-attributes"></a>编辑受支持属性的列表

给定应用程序支持的用户属性是预先配置的。 大多数应用程序的用户管理 Api 不支持架构发现。 因此，Azure AD 预配服务无法通过调用应用程序来动态生成受支持的属性列表。

但是，某些应用程序支持自定义属性，Azure AD 预配服务可以读取和写入自定义属性。 若要将其定义输入到 Azure 门户中，请选中 "**属性映射**" 屏幕底部的 "**显示高级选项**" 复选框，然后选择 "编辑应用的**属性列表**"。

支持属性列表自定义的应用程序和系统包括：

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory（支持 [Azure AD 图形 API 默认属性](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)和自定义目录扩展）
- 支持 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的应用（需要添加[核心架构](https://tools.ietf.org/html/rfc7643)中定义的属性）

> [!NOTE]
> 建议只让自定义了其应用程序和系统的架构，并且原本就知道自定义属性定义方式的管理员来编辑受支持属性的列表。 有时，需要熟悉应用程序或系统提供的 API 和开发人员工具才能执行此操作。

编辑受支持属性的列表时，将提供以下属性：

- **名称** - 属性的系统名称，在目标对象的架构中定义。
- **类型**-属性存储的数据的类型，在目标对象的架构中定义，可以是以下类型之一：
  - *二进制* - 属性包含二进制数据。
  - *布尔值* - 属性包含 True 或 False 值。
  - *日期时间* - 属性包含日期字符串。
  - *整数* - 属性包含整数。
  - *引用* - 属性包含 ID，该 ID 引用目标应用程序中的另一个表存储的值。
  - *字符串* - 属性包含文本字符串。
- **主键?** -特性是否定义为目标对象的架构中的主键字段。
- **必需？** -是否需要在目标应用程序或系统中填充属性。
- **多值?** -特性是否支持多个值。
- **区分大小写?** -是否以区分大小写的方式对属性值进行求值。
- **API 表达式**-不使用，除非通过特定预配连接器（如 Workday）的文档的指示执行此操作。
- **被引用的对象特性**-如果它是引用类型属性，则可以使用此菜单选择包含与属性关联的值的目标应用程序中的表和属性。 例如，如果名为“Department”的属性的存储值引用了独立“Departments”表中的对象，则需要选择“Departments.Name”。 给定应用程序支持的引用表和主要 ID 字段是预先配置的，目前无法使用 Azure 门户进行编辑，但可以使用[图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)进行编辑。

若要添加新属性，请滚动到受支持属性列表的末尾，使用提供的输入填充上述字段，然后选择“添加属性”。 添加完属性后选择“保存”。 然后，需要重新加载 "**预配**" 选项卡，以便新属性在 "属性映射编辑器" 中变得可用。

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>还原默认属性和属性映射

如果需要重新启动现有映射并将其重置回默认状态，可以选中 "**还原默认映射**" 复选框并保存配置。 这样做会设置所有映射，就像应用程序库中刚添加到 Azure AD 租户的应用程序一样。

如果选择此选项，则在预配服务正在运行时，会有效地强制执行所有用户的重新同步。

> [!IMPORTANT]
> 在调用此选项之前，强烈建议将**预配状态**设置为 "**关闭**"。

## <a name="what-you-should-know"></a>要点

- Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。
- 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。
- 建议的最佳做法是将连续更改的次数保持在最少的属性映射。
- 由于不能指定用于同步照片的格式，因此目前不支持将预配照片属性添加到应用。 可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求该功能
- 特性 IsSoftDeleted 通常是应用程序的默认映射的一部分。 在以下四种情况中的一种情况下，IsSoftdeleted 可能为 true （用户因未分配到应用程序而超出范围，用户由于未满足范围筛选器而超出范围，用户已在 Azure AD 中软删除，或属性 AccountEnabled 设置为 false 在用户上）。 
- Azure AD 预配服务不支持预配 null 值

## <a name="next-steps"></a>后续步骤

- [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
- [为属性映射编写表达式](functions-for-customizing-application-data.md)
- [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](use-scim-to-provision-users-and-groups.md)
- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
