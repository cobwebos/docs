---
title: 自定义 Azure AD 属性映射 |Microsoft Docs
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
ms.openlocfilehash: 4abfdd94c57064c86e533234d78f774c45ba8e4a
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593856"
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

1. 选择**映射**配置以打开相关的**属性映射**屏幕。 要使 SaaS 应用程序正常运行，需要一些属性映射。 对于必需属性，“删除”功能将不可用****。

   ![使用属性映射来配置应用的属性映射](./media/customize-application-attributes/22.png)

   在此屏幕截图中，可以看到，Salesforce 中托管对象的**Username**属性用链接的 Azure Active Directory 对象的**userPrincipalName**值填充。

1. 选择现有的**属性映射**，以打开 "**编辑属性**" 屏幕。 在此处可以编辑在 Azure AD 和目标应用程序之间流动的用户属性。

   ![使用编辑属性编辑用户属性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解属性映射类型

使用属性映射可以控制属性在第三方 SaaS 应用程序中的填充方式。
支持四种不同的映射类型：

- **直接** - 目标属性使用 Azure AD 中的链接对象的属性值填充。
- **常量**-目标属性使用指定的特定字符串填充。
- **表达式** - 目标属性基于类似脚本的表达式的结果进行填充。
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)。
- **无** - 目标属性保持不变。 但是，如果目标属性曾经为空，则会用您指定的默认值进行填充。

除了这四种基本类型外，自定义属性映射还支持可选**默认**值赋值的概念。 如果在 Azure AD 或目标对象上没有值，则默认值分配将确保用值填充目标属性。 最常见的配置是将此项留空。

### <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

在上一节中，已将你引入 "属性映射类型" 属性。
除了此属性，属性映射还支持以下属性：

- **源属性** - 来自源系统的用户属性（例如 Azure Active Directory）。
- **目标属性** – 目标系统中的用户属性（例如 ServiceNow）。
- **默认值（可选）** -如果源属性为 null，则将传递给目标系统的值。 只有在创建用户时才会设置此值。 更新现有用户时，不会设置 "默认值 when null"。 例如，如果你想要使用特定职务（当它在源系统中为 null 时）来预配目标系统中的所有现有用户，则可以使用以下[表达式](../app-provisioning/functions-for-customizing-application-data.md)： Switch （IsPresent （[jobTitle]）、"DefaultValue"、"True"、[jobTitle]）。 请确保将 "默认值" 替换为源系统中的 null 值时要设置的值。 
- **使用此属性匹配对象**–是否应使用此映射唯一标识源系统和目标系统之间的用户。 它通常在 Azure AD 中的 userPrincipalName 或 mail 属性上设置，该属性通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性****。 如果有多个，则按此字段定义的顺序对它们进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。 虽然您可以根据需要设置任意多个匹配属性，但请考虑您用作匹配属性的属性是否确实是唯一的，以及是否需要匹配属性。 通常，客户在其配置中有1个或2个匹配属性。 
- **应用此映射**
  - **始终**–在用户的创建和更新操作中应用此映射。
  - **仅在创建过程中**-仅在用户创建操作时应用此映射。

## <a name="matching-users-in-the-source-and-target--systems"></a>匹配源系统和目标系统中的用户
可以在 "领域" 方案（其中用户不会在目标系统中退出）和 "要重建 mqtt" 方案（其中用户已存在于目标系统中）部署 "Azure AD 预配服务"。 为了同时支持这两种方案，预配服务使用匹配属性的概念。 匹配属性允许您确定如何唯一标识源中的用户并匹配目标中的用户。 在规划部署的过程中，确定可用于唯一标识源系统和目标系统中的用户的属性。 注意事项：

- **匹配的属性应是唯一的：** 客户通常使用 userPrincipalName、mail 或对象 ID 等属性作为匹配属性。
- **多个属性可用作匹配属性：** 您可以定义要在匹配用户和计算顺序时进行评估的多个属性（在 UI 中定义为匹配优先级）。 例如，如果将三个属性定义为匹配属性，并且在计算前两个属性后，用户将被唯一匹配，则该服务将不会计算第三个属性。 服务将按指定的顺序评估匹配的属性，并在找到匹配项时停止计算。  
- **源和目标中的值不必完全匹配：** 目标中的值可以是源中值的一些简单函数。 因此，可以在源和目标中有一个 emailAddress 属性，并按 emailAddress 属性的函数进行匹配，将一些字符替换为一些常量值。  
- **不支持基于属性组合的匹配：** 大多数应用程序不支持基于两个属性进行查询。 因此，不能基于特性的组合匹配。 可以在一个之后计算单个属性。
- **所有用户都必须具有至少一个匹配属性的值：** 如果定义了一个匹配属性，则所有用户都必须具有源系统中该属性的值。 例如，如果您将 userPrincipalName 定义为匹配属性，则所有用户都必须具有 userPrincipalName。 如果定义多个匹配属性（例如 extensionAttribute1 和 mail），则并非所有用户都必须具有相同的匹配属性。 一个用户可以有一个 extensionAttribute1，而不是邮件，而另一个用户可以拥有邮件，但不能包含 extensionAttribute1。 
- **目标应用程序必须支持对匹配属性进行筛选：** 应用程序开发人员允许在其用户或组 API 上筛选属性的子集。 对于库中的应用程序，我们确保默认属性映射适用于目标应用程序的 API 支持筛选的属性。 更改目标应用程序的默认匹配属性时，请检查第三方 API 文档以确保可以筛选属性。  

## <a name="editing-group-attribute-mappings"></a>编辑组属性映射

选定数量的应用程序（如 ServiceNow、Box 和 G Suite）支持预配组对象和用户对象。 组对象可以包含组属性（如显示名称和电子邮件别名）以及组成员。

![示例显示具有预配组和用户对象的 ServiceNow](./media/customize-application-attributes/24.png)

可以选择启用或禁用组设置，方法是在 "**映射**" 下选择组映射，并在 "**属性映射**" 屏幕中选择 "**启用**" 选项。

可以自定义预配为“组”对象的一部分的属性，就像前面所述的预配“用户”对象一样。 

> [!TIP]
> 预配组对象（属性和成员）的过程完全不同于[将组分配](../manage-apps/assign-user-or-group-access-portal.md)到应用程序。 可以将组分配到应用程序，但只能预配组中包含的用户对象。 预配整个组对象不需要使用分配中的组。

## <a name="editing-the-list-of-supported-attributes"></a>编辑受支持属性的列表

给定应用程序支持的用户属性是预先配置的。 大多数应用程序的用户管理 Api 不支持架构发现。 因此，Azure AD 预配服务无法通过调用应用程序来动态生成受支持的属性列表。

但是，某些应用程序支持自定义属性，Azure AD 预配服务可以读取和写入自定义属性。 若要将其定义输入到 Azure 门户中，请选中 "**属性映射**" 屏幕底部的 "**显示高级选项**" 复选框，然后选择 "编辑应用的**属性列表**"。

支持属性列表自定义的应用程序和系统包括：

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory （[Microsoft Graph REST API 1.0 版引用](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)和自定义目录扩展支持）
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
- **主键？** -特性是否定义为目标对象的架构中的主键字段。
- **必填？** -是否需要在目标应用程序或系统中填充属性。
- **多值？** -特性是否支持多个值。
- **区分大小写?** -是否以区分大小写的方式对属性值进行求值。
- **API 表达式**-不使用，除非通过特定预配连接器（如 Workday）的文档的指示执行此操作。
- **被引用的对象特性**-如果它是引用类型属性，则可以使用此菜单选择包含与属性关联的值的目标应用程序中的表和属性。 例如，如果名为“Department”的属性的存储值引用了独立“Departments”表中的对象，则需要选择“Departments.Name”。 给定应用程序支持的引用表和主要 ID 字段是预先配置的，目前无法使用 Azure 门户进行编辑，但可以使用[MICROSOFT GRAPH API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)进行编辑。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>将自定义扩展属性预配到符合 SCIM 的应用程序
SCIM RFC 定义核心用户和组架构，同时允许对架构进行扩展，以满足应用程序的需求。 向 SCIM 应用程序添加自定义属性：
   1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)，选择 "**企业应用程序**"，选择应用程序，然后选择 "**设置**"。
   2. 在 "**映射**" 下，选择要为其添加自定义属性的对象（用户或组）。
   3. 在页面底部，选择 "**显示高级选项**"。
   4. 选择 "**编辑 AppName 的属性列表**"。
   5. 在属性列表的底部，在提供的字段中输入自定义属性的相关信息。 然后选择 "**添加属性**"。

对于 SCIM 应用程序，属性名称必须遵循以下示例中所示的模式。 可以根据应用程序的要求自定义 "CustomExtensionName" 和 "CustomAttribute"，例如：  
 * urn： ietf： params： scim：架构：扩展： CustomExtensionName：2.0： User： CustomAttribute 
 * urn： ietf： params： scim：架构：扩展：2.0： CustomExtensionName： CustomAttribute  
 * urn： ietf： params： scim：架构：扩展： CustomExtensionName：2.0： CustomAttributeName：值

这些说明仅适用于启用了 SCIM 的应用程序。 使用 SCIM 的应用程序（如 ServiceNow 和 Salesforce）不与 Azure AD 集成，因此，在添加自定义属性时，它们不需要此特定的命名空间。

自定义属性不能是引用属性或多值属性。 当前，库中的应用程序仅支持自定义多值扩展属性。  
 
**具有扩展属性的用户的示例表示形式：**

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
使用以下步骤将用户的角色预配到应用程序。 请注意，以下说明特定于自定义的 SCIM 应用程序。 对于 Salesforce 和 ServiceNow 等库应用程序，请使用预定义的角色映射。 以下项目符号介绍了如何将 AppRoleAssignments 属性转换为应用程序所需的格式。

- 将 Azure AD 中的 appRoleAssignment 映射到应用程序中的某个角色需要使用[表达式](../app-provisioning/functions-for-customizing-application-data.md)来转换该特性。 **不应将 appRoleAssignment 特性直接映射**到 role 特性，而无需使用表达式来分析角色的详细信息。 

- **SingleAppRoleAssignment** 
  - **何时使用：** 使用 SingleAppRoleAssignment 表达式为用户设置单个角色并指定主角色。 
  - **如何配置：** 使用上述步骤导航到 "属性映射" 页，并使用 SingleAppRoleAssignment 表达式映射到 roles 属性。 有三个角色属性可供选择：（角色 [主 eq "True"]。显示、角色 [主 eq "True"）、类型和角色 [主 eq "True"]。 你可以选择在映射中包含任何或所有角色属性。 如果要包含多个映射，只需添加一个新映射，并将其包含为目标属性即可。  
  
  ![添加 SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **要考虑的事项**
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
  
- **AppRoleAssignmentsComplex** 
  - **何时使用：** 使用 AppRoleAssignmentsComplex 表达式为用户预配多个角色。 
  - **如何配置：** 如以上所述编辑受支持属性的列表，以便为角色包含一个新属性： 
  
    ![添加角色](./media/customize-application-attributes/add-roles.png)<br>

    然后，使用 AppRoleAssignmentsComplex 表达式映射到自定义角色属性，如下图所示：

    ![添加 AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **要考虑的事项**
    - 所有角色都将设置为主 = false。
    - POST 包含角色类型。 修补请求不包含类型。 我们正在努力发送 POST 和 PATCH 请求中的类型。
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>设置多值属性
某些属性（如 phoneNumbers 和电子邮件）是多值属性，可能需要指定不同类型的电话号码或电子邮件。 对于多值属性，请使用下面的表达式。 它允许您指定属性类型，并将其映射到值的相应 Azure AD 用户特性。 

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

如果需要重新启动现有映射并将其重置回默认状态，可以选中 "**还原默认映射**" 复选框并保存配置。 这样做会设置所有映射和范围筛选器，就像应用程序库中刚添加到 Azure AD 租户的应用程序一样。

如果选择此选项，则在预配服务正在运行时，会有效地强制执行所有用户的重新同步。

> [!IMPORTANT]
> 在调用此选项之前，强烈建议将**预配状态**设置为 "**关闭**"。

## <a name="what-you-should-know"></a>要点

- Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。
- 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。
- 建议的最佳做法是将连续更改的次数保持在最少的属性映射。
- 由于不能指定用于同步照片的格式，因此目前不支持将预配照片属性添加到应用。 可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求该功能
- 特性 IsSoftDeleted 通常是应用程序的默认映射的一部分。 在以下四种情况中的一种情况下，IsSoftdeleted 可能为 true （用户因未分配到应用程序而超出范围，用户由于未满足范围筛选器而超出范围，用户已软删除在 Azure AD 中，或者在用户的属性 AccountEnabled 设置为 false）。 不建议从属性映射中删除 IsSoftDeleted 属性。
- Azure AD 预配服务不支持预配 null 值。
- 它们的主键通常是 "ID"，不应作为属性映射中的目标属性包括在内。 
- 角色属性通常需要使用表达式而不是直接映射进行映射。 有关角色映射的更多详细信息，请参阅上面的部分。 

## <a name="next-steps"></a>后续步骤

- [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
- [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)
- [用于用户预配的作用域筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](use-scim-to-provision-users-and-groups.md)
- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
