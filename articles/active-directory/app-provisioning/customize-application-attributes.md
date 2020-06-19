---
title: 自定义 Azure AD 属性映射 | Microsoft Docs
description: 了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足业务需求。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.openlocfilehash: 9697bc2abd147a501466ba134f96512fe15173c0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639055"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射

Microsoft Azure AD 支持将用户预配到第三方 SaaS 应用程序，例如 Salesforce、G Suite 等等。 如果为第三方 SaaS 应用程序启用用户预配，Azure 门户将通过属性映射控制其属性值。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在一组预先配置的属性和属性映射。 某些应用与用户一起管理其他类型的对象，例如“组”。

可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射，或创建新的属性映射。

## <a name="editing-user-attribute-mappings"></a>编辑用户属性映射

按照以下步骤访问用户预配的“映射”功能：

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择“企业应用程序”。 系统显示所有已配置的应用（包括从库中添加的应用）的列表。
1. 选择任何应用以加载其应用管理窗格，可在其中查看报告和管理应用设置。
1. 选择“预配”管理所选应用的用户帐户预配设置。
1. 展开“映射”，查看和编辑在 Azure AD 和目标应用程序之间流动的用户属性。 可在本部分中选择性地配置组和用户帐户的预配（如果目标应用程序支持这样做）。

   ![使用映射查看和编辑用户属性](./media/customize-application-attributes/21.png)

1. 选择“映射”配置，打开相关的“属性映射”屏幕。 SaaS 应用程序需要执行一些属性映射才能正确运行。 对于必需属性，“删除”功能将不可用。

   ![使用属性映射来配置应用的属性映射](./media/customize-application-attributes/22.png)

   在此屏幕截图中，可以发现 Salesforce 中托管对象的“用户名”属性由所链接的 Azure Active Directory 对象的“userPrincipalName”值填充。

1. 选择现有“属性映射”以打开“编辑属性”屏幕。 可在此屏幕中编辑在 Azure AD 和目标应用程序之间流动的用户属性。

   ![使用“编辑属性”编辑用户属性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解属性映射类型

使用属性映射可以控制属性在第三方 SaaS 应用程序中的填充方式。
支持四种不同的映射类型：

- **直接** – 目标属性由 Azure AD 中的链接对象的属性值填充。
- 常量 - 目标属性使用你指定的特定字符串填充。
- **表达式** - 目标属性是基于脚本式表达式的结果填充的。
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)。
- **无** - 目标属性保持不变。 不过，如果目标属性曾为空，则它由你指定的默认值填充。

除了这四个基本类型之外，自定义属性映射还支持可选的默认值赋值概念。 默认值赋值确保当 Azure AD 或目标对象中没有值时使用某个值填充目标属性。 最常见的配置是将此项留空。

### <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

前面的部分介绍了属性映射类型属性。
除此属性外，属性映射还支持以下属性：

- **源属性** - 来自源系统的用户属性（示例：Azure Active Directory）。
- **目标属性** - 目标系统中的用户属性（示例：ServiceNow）。
- 为 NULL 时填入默认值（可选） - 如果源属性为 NULL，将传递给目标系统的值。 只有在创建用户时才会预配此值。 更新现有用户时，不会预配“为 NULL 时填入默认值”。 例如，如果你想要使用特定职务来预配目标系统中的所有现有用户（在源系统中为 NULL 时），则可以使用以下[表达式](../app-provisioning/functions-for-customizing-application-data.md)：Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). 确保将“默认值”替换为源系统中为 NULL 时需要预配的值。 
- 使用此属性匹配对象 - 是否应使用此映射唯一标识源系统和目标系统之间的用户。 这通常在 Azure AD 中的 userPrincipalName 或mail 属性上设置，通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性。 当存在多个匹配属性时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。 虽然可以根据需要设置任意多个匹配属性，但请考虑你用作匹配属性的属性是否确实是唯一的，以及是否需要匹配属性。 通常，客户在其配置中有 1 到 2 个匹配属性。 
- **应用此映射**
  - 始终 - 对用户创建和更新操作均应用此映射。
  - 仅创建期间 - 仅对用户创建操作应用此映射。

## <a name="matching-users-in-the-source-and-target--systems"></a>匹配源系统和目标系统中的用户
可以在“绿地”场景（用户不会退出目标系统）和“棕地”场景（用户已存在于目标系统中）中部署 Azure AD 预配服务。 为了同时支持这两种场景，预配服务使用匹配属性的概念。 可以通过匹配属性确定如何唯一标识源中的用户，并匹配目标中的用户。 在规划部署期间，确定可用于唯一标识源系统和目标系统中的用户的属性。 注意事项：

- 匹配属性应是唯一的：客户通常使用 userPrincipalName、mail 或 object ID 等属性作为匹配属性。
- 多个属性可用作匹配属性：可以定义在匹配用户时要评估的多个属性，以及评估的顺序（在 UI 中定义为匹配优先级）。 例如，如果将三个属性定义为匹配属性，并且在评估前两个属性后，用户唯一匹配，则该服务将不会评估第三个属性。 服务将按指定的顺序评估匹配属性，并在找到匹配项时停止评估。  
- 源和目标中的值不必完全匹配：目标中的值可以是源中值的某个简单函数。 因此，可以在源中使用 emailAddress 属性，在目标中使用 userPrincipalName，并通过 emailAddress 属性的函数进行匹配，该属性将一些字符替换为一些常量值。  
- 不支持基于属性组合的匹配：大多数应用程序不支持基于两个属性进行查询。 因此，不能基于属性组合进行匹配。 可以逐个评估单个属性。
- 所有用户都必须为至少一个匹配属性提供一个值：如果定义了一个匹配属性，那么源系统中的所有用户都必须为该属性提供一个值。 例如，如果你将 userPrincipalName 定义为匹配属性，则所有用户都必须具有 userPrincipalName。 如果定义多个匹配属性（例如 extensionAttribute1 和 mail），则并非所有用户都必须具有相同的匹配属性。 一个用户可以有 extensionAttribute1，但没有mail，而另一个用户可以有 mail，但没有 extensionAttribute1。 
- 目标应用程序必须支持对匹配属性进行筛选：应用程序开发人员允许在其用户或组 API 上筛选属性的子集。 对于库中的应用程序，我们确保默认属性映射适用于目标应用程序的 API 支持筛选的属性。 更改目标应用程序的默认匹配属性时，请检查第三方 API 文档以确保可以筛选该属性。  

## <a name="editing-group-attribute-mappings"></a>编辑组属性映射

一些特定的应用程序（例如 ServiceNow、Box 和 G Suite）支持预配“组”对象和“用户”对象。 “组”对象可以包含组属性（例如显示名称和电子邮件别名）以及组成员。

![示例显示具有预配“组”和“用户”对象的 ServiceNow](./media/customize-application-attributes/24.png)

可以通过在“映射”下面选择组映射，并在“属性映射”屏幕中将“已启用”设置为所需的选项，以选择性地启用或禁用组预配。

可以自定义预配为“组”对象的一部分的属性，就像前面所述的预配“用户”对象一样。 

> [!TIP]
> 预配组对象（属性和成员）的过程完全不同于[将组分配](../manage-apps/assign-user-or-group-access-portal.md)到应用程序。 可以将组分配到应用程序，但只能预配组中包含的用户对象。 预配整个组对象不需要使用分配中的组。

## <a name="editing-the-list-of-supported-attributes"></a>编辑受支持属性的列表

给定应用程序支持的用户属性是预先配置的。 大多数应用程序的用户管理 API 不支持架构发现。 因此，Azure AD 预配服务无法通过调用应用程序来动态生成支持的属性列表。

但是，某些应用程序支持自定义属性，Azure AD 预配服务可以读取和写入自定义属性。 若要将其定义输入到 Azure 门户中，请选中“属性映射”屏幕底部的“显示高级选项”复选框，然后选择应用的“编辑属性列表”。

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
- 类型 - 属性存储的数据类型，在目标对象的架构中定义，可以是以下类型之一：
  - *二进制* - 属性包含二进制数据。
  - *布尔值* - 属性包含 True 或 False 值。
  - *日期时间* - 属性包含日期字符串。
  - *整数* - 属性包含整数。
  - *引用* - 属性包含 ID，该 ID 引用目标应用程序中的另一个表存储的值。
  - *字符串* - 属性包含文本字符串。
- **主键?** - 属性是否已定义为目标对象架构中的主键字段。
- **必需？** - 是否需要在目标应用程序或系统中填充该属性。
- **多值?** - 属性是否支持多个值。
- **区分大小写?** - 是否以区分大小写的方式计算属性值。
- API 表达式 - 除非特定预配连接器（例如 Workday）的文档要求使用，否则请不要使用。
- 引用的对象属性 - 如果这是一个引用类型的属性，则可以通过此菜单在目标应用程序中选择包含与该属性关联的值的表和属性。 例如，如果名为“Department”的属性的存储值引用了独立“Departments”表中的对象，则需要选择“Departments.Name”。 给定应用程序支持的引用表和主要 ID 字段是预先配置的，目前无法使用 Azure 门户进行编辑，但可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes) 进行编辑。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>将自定义扩展属性预配到符合 SCIM 的应用程序
SCIM RFC 定义一个核心用户和组模式，同时还允许对模式进行扩展，以满足应用程序的需要。 向 SCIM 应用程序添加自定义属性：
   1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com)，选择“企业应用程序”，选择你的应用程序，然后选择“预配”。
   2. 在“映射”下，选择要为其添加自定义属性的对象（用户或组）。
   3. 在页面底部，选择“显示高级选项”。
   4. 选择“编辑 AppName 的属性列表”。
   5. 在属性列表底部，在提供的字段中输入有关自定义属性的信息。 然后选择“添加属性”。

对于 SCIM 应用程序，属性名称必须遵循以下示例所示的模式。 可以根据应用程序的要求自定义“CustomExtensionName”和“CustomAttribute”，例如：  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

这些说明仅适用于启用了 SCIM 的应用程序。 诸如 ServiceNow 和 Salesforce 之类的应用程序不与使用 SCIM 的 Azure AD 集成，因此它们在添加自定义属性时不需要这一特定的命名空间。

自定义属性不能是引用属性或多值属性。 当前，库中的应用程序仅支持自定义多值扩展属性。  
 
具有扩展属性的用户的示例表示形式：

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>将角色预配到 SCIM 应用
使用以下步骤将用户角色预配到应用程序。 请注意，以下说明特定于自定义 SCIM 应用程序。 对于 Salesforce 和 ServiceNow 之类的库应用程序，请使用预定义的角色映射。 下面重点说明了如何将 AppRoleAssignments 属性转换为应用程序所需的格式。

- 将 Azure AD 中的 appRoleAssignment 映射到应用程序中的某个角色需要使用[表达式](../app-provisioning/functions-for-customizing-application-data.md)来转换该属性。 如果不使用表达式来分析角色详细信息，就不应将 appRoleAssignment 属性直接映射到角色属性。 

- **SingleAppRoleAssignment** 
  - **何时使用：** 使用 SingleAppRoleAssignment 表达式为用户预配单个角色，并指定主要角色。 
  - **配置方式：** 使用上述步骤导航到“属性映射”页，并使用 SingleAppRoleAssignment 表达式映射到角色属性。 有三个角色属性可供选择：(roles[primary eq "True"].display、roles[primary eq "True].type 和 roles[primary eq "True"].value)。 可以选择在映射中包含任何或所有角色属性。 如果要包含多个属性，只需添加一个新映射，并将其包含为目标属性即可。  
  
  ![添加 SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - 注意事项
    - 确保未向用户分配多个角色。 我们无法保证将预配哪个角色。
    
  - **示例输出** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- AppRoleAssignmentsComplex 
  - **何时使用：** 使用 AppRoleAssignmentsComplex 表达式为用户预配多个角色。 
  - **配置方式：** 根据上述说明编辑支持的属性列表，以便为角色包含一个新属性： 
  
    ![添加角色](./media/customize-application-attributes/add-roles.png)<br>

    然后，使用 AppRoleAssignmentsComplex 表达式映射到自定义角色属性，如下图所示：

    ![添加 AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - 注意事项
    - 所有角色都将预配为主要角色 = false。
    - POST 包含角色类型。 PATCH 请求不包含类型。 我们正致力于在 POST 和 PATCH 请求中发送类型。
    
  - **示例输出** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>预配多值属性
某些属性（如 phoneNumbers 和 emails）是多值属性，可能需要指定不同类型的电话号码或电子邮件。 对于多值属性，请使用下面的表达式。 它允许你指定属性类型，并将其映射到值对应的 Azure AD 用户属性。 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>还原默认属性和属性映射

如果需要从头开始并将现有映射重置为默认状态，可以选中“还原默认映射”复选框并保存配置。 这样就会设置所有映射和范围筛选器，就如同刚刚将应用程序从应用程序库添加到 Azure AD 租户一样。

在运行预配服务时，选择此选项可以有效地强制重新同步所有用户。

> [!IMPORTANT]
> 调用此选项之前，强烈建议将“预配状态”设置为“关闭”。

## <a name="what-you-should-know"></a>要点

- Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。
- 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。
- 建议采用的最佳做法是尽量少地连续更改属性映射。
- 由于不能指定用于同步照片的格式，因此目前不支持添加预配到应用的照片属性。 可以在 [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) 上请求该功能。
- IsSoftDeleted 属性通常是应用程序默认映射的一部分。 IsSoftdeleted 在以下四种情况之一可能为 true（用户由于未从应用程序分配而超出范围，用户由于未满足范围筛选器而超出范围，用户已在 Azure AD 中被软删除，或者用户的 AccountEnabled 属性设置为 false）。 不建议从属性映射中删除 IsSoftDeleted 属性。
- Azure AD 预配服务不支持预配 NULL 值。
- 它们的主键通常为“ID”，不应作为目标属性包含在属性映射中。 
- 角色属性通常需要使用表达式进行映射，而不是直接映射。 有关角色映射的更多详细信息，请参阅上面的部分。 

## <a name="next-steps"></a>后续步骤

- [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
- [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)
- [用于用户预配的作用域筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](use-scim-to-provision-users-and-groups.md)
- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
