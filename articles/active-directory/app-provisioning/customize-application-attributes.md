---
title: 自定义 Azure AD 属性映射 |微软文档
description: 了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足业务需求。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7003899b59e409a785c3a50e89aae6674e377b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264084"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure 活动目录中的 SaaS 应用程序自定义用户预配属性映射

Microsoft Azure AD 支持用户预配到第三方 SaaS 应用程序，如 Salesforce、G Suite 等。 如果为第三方 SaaS 应用程序启用用户预配，Azure 门户将通过属性映射控制其属性值。

Azure AD 用户对象和每个 SaaS 应用的用户对象之间有一组预先配置的属性和属性映射。 某些应用与"用户"一起管理其他类型的对象，如"组"。

可以根据业务需求自定义默认的属性映射。 因此，您可以更改或删除现有的属性映射，或创建新的属性映射。

## <a name="editing-user-attribute-mappings"></a>编辑用户属性映射

按照以下步骤访问用户预配的**映射**功能：

1. 登录到 Azure[活动目录门户](https://aad.portal.azure.com)。
1. 从左侧窗格中选择**企业应用程序**。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用以加载其应用管理窗格，您可以在其中查看报表和管理应用设置。
1. 选择 **"预配**"以管理所选应用的用户帐户预配设置。
1. 展开**映射**以查看和编辑在 Azure AD 和目标应用程序之间流淌的用户属性。 如果目标应用程序支持它，则此部分允许您选择配置组和用户帐户的预配。

   ![使用映射查看和编辑用户属性](./media/customize-application-attributes/21.png)

1. 选择**映射**配置以打开相关**属性映射**屏幕。 SaaS 应用程序需要一些属性映射才能正常运行。 对于必需属性，“删除”功能将不可用****。

   ![使用属性映射配置应用的属性映射](./media/customize-application-attributes/22.png)

   在此屏幕截图中，您可以看到 Salesforce 中托管对象的**用户名**属性与链接的 Azure 活动目录对象的**用户主体名称**值一起填充。

1. 选择现有**属性映射**以打开 **"编辑属性"** 屏幕。 在这里，您可以编辑在 Azure AD 和目标应用程序之间流动的用户属性。

   ![使用编辑属性编辑用户属性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解属性映射类型

使用属性映射可以控制属性在第三方 SaaS 应用程序中的填充方式。
支持四种不同的映射类型：

- **直接** - 目标属性使用 Azure AD 中的链接对象的属性值填充。
- **常量**= 目标属性使用您指定的特定字符串填充。
- **表达式** - 目标属性基于类似脚本的表达式的结果进行填充。
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)。
- **无** - 目标属性保持不变。 但是，如果目标属性为空，则使用指定的 Default 值填充它。

除了这四种基本类型外，自定义属性映射还支持可选**默认值**赋值的概念。 如果 Azure AD 或目标对象上没有值，则默认值赋值可确保使用值填充目标属性。 最常见的配置是将此项留空。

### <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

在上一节中，您已引入属性映射类型属性。
除了此属性外，属性映射还支持以下属性：

- **源属性** - 来自源系统的用户属性（例如 Azure Active Directory）。
- **目标属性** – 目标系统中的用户属性（例如 ServiceNow）。
- **默认值（如果为空）** - 如果源属性为空，则传递给目标系统的值。 仅当创建用户时，才会预配此值。 更新现有用户时不会预配"当为空时的默认值"。 例如，如果要向目标系统中的所有现有用户预配特定的作业标题（当该名称在源系统中为空时），可以使用以下[表达式](../app-provisioning/functions-for-customizing-application-data.md)：Switch（Is存在[作业标题]）、"默认值"、"真实"、"作业标题]"。 请确保在源系统中为空时，将"默认值"替换为要预配的内容。 
- **匹配使用此属性的对象**— 是否应使用此映射来唯一标识源和目标系统之间的用户。 它通常设置在 Azure AD 中的用户主体名称或邮件属性上，该属性通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性****。 当有多个时，按此字段定义的顺序计算它们。 一旦找到匹配，就不会进一步评估其他匹配属性。
- **应用此映射**
  - **始终**= 在用户创建和更新操作上应用此映射。
  - **仅在创建期间**- 仅在用户创建操作上应用此映射。

## <a name="matching-users-in-the-source-and-target--systems"></a>在源和目标系统中匹配用户
Azure AD 预配服务可以部署在"绿地"方案中（用户不会在目标系统中退出）和"棕色字段"方案（目标系统中用户已经存在）。 为了支持这两种情况，预配服务使用匹配属性的概念。 匹配属性允许您确定如何唯一标识源中的用户并匹配目标中的用户。 在规划部署时，确定可用于唯一标识源和目标系统中的用户的属性。 需要注意的事项：

- **匹配属性应是唯一的：** 客户通常使用用户主体名称、邮件或对象 ID 等属性作为匹配属性。
- **多个属性可用作匹配属性：** 您可以定义在匹配用户和计算用户的顺序时要计算的多个属性（定义为 UI 中的匹配优先级）。 例如，如果将三个属性定义为匹配属性，并且用户在计算前两个属性后唯一匹配，则服务将不会计算第三个属性。 该服务将按指定的顺序计算匹配属性，并在找到匹配项时停止评估。  
- **源和目标中的值不必完全匹配：** 目标中的值可以是源中值的一些简单函数。 因此，可以在源中具有电子邮件地址属性，在目标中具有用户主体名称，并通过电子邮件地址属性的函数进行匹配，该函数将某些字符替换为一些常量值。  
- **不支持基于属性组合的匹配：** 大多数应用程序不支持基于两个属性的查询。 因此，无法基于属性的组合进行匹配。 可以一个接一个地计算单个属性。
- **所有用户必须至少具有一个匹配属性的值：** 如果定义一个匹配属性，则所有用户都必须在源系统中具有该属性的值。 例如，如果将用户主体名称定义为匹配属性，则所有用户都必须具有用户主体名称。 如果定义多个匹配属性（例如扩展属性1 和邮件），则并非所有用户必须具有相同的匹配属性。 一个用户可以有一个扩展属性1，但不能邮寄，而另一个用户可以有邮件，但没有扩展属性1。 
- **目标应用程序必须支持对匹配属性进行筛选：** 应用程序开发人员允许筛选其用户或组 API 上的属性子集。 对于库中的应用程序，我们确保默认属性映射适用于目标应用程序的 API 确实支持筛选的属性。 更改目标应用程序的默认匹配属性时，请检查第三方 API 文档以确保可以筛选该属性。  

## <a name="editing-group-attribute-mappings"></a>编辑组属性映射

选定的许多应用程序（如 ServiceNow、Box 和 G Suite）支持预配组对象和用户对象的能力。 组对象可以包含组属性，如显示名称和电子邮件别名以及组成员。

![示例显示服务现在与预配组和用户对象](./media/customize-application-attributes/24.png)

通过选择 **"映射**"下的组映射，并将 **"已启用"** 设置为 **"属性映射**"屏幕中所需的选项，可以选择启用或禁用组预配。

可以自定义预配为“组”对象的一部分的属性，就像前面所述的预配“用户”对象一样。 

> [!TIP]
> 预配组对象（属性和成员）的过程完全不同于[将组分配](../manage-apps/assign-user-or-group-access-portal.md)到应用程序。 可以将组分配到应用程序，但只能预配组中包含的用户对象。 预配整个组对象不需要使用分配中的组。

## <a name="editing-the-list-of-supported-attributes"></a>编辑受支持属性的列表

给定应用程序支持的用户属性是预先配置的。 大多数应用程序的用户管理 API 不支持架构发现。 因此，Azure AD 预配服务无法通过调用应用程序来动态生成受支持的属性列表。

但是，某些应用程序支持自定义属性，Azure AD 预配服务可以读取和写入自定义属性。 要在 Azure 门户中输入其定义，请在 **"属性映射"** 屏幕底部选中"**显示高级选项**"复选框，然后为应用选择 **"编辑属性列表**"。

支持属性列表自定义的应用程序和系统包括：

- Salesforce
- ServiceNow
- Workday
- Azure 活动目录（支持[Microsoft 图形 REST API v1.0 引用](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)和自定义目录扩展）
- 支持 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的应用（需要添加[核心架构](https://tools.ietf.org/html/rfc7643)中定义的属性）

> [!NOTE]
> 建议只让自定义了其应用程序和系统的架构，并且原本就知道自定义属性定义方式的管理员来编辑受支持属性的列表。 有时，需要熟悉应用程序或系统提供的 API 和开发人员工具才能执行此操作。

编辑受支持属性的列表时，将提供以下属性：

- **名称** - 属性的系统名称，在目标对象的架构中定义。
- **类型**- 属性存储的数据类型，如目标对象的架构中定义，可以是以下类型之一：
  - *二进制* - 属性包含二进制数据。
  - *布尔值* - 属性包含 True 或 False 值。
  - *日期时间* - 属性包含日期字符串。
  - *整数* - 属性包含整数。
  - *引用* - 属性包含 ID，该 ID 引用目标应用程序中的另一个表存储的值。
  - *字符串* - 属性包含文本字符串。
- **主钥匙？** - 属性是否定义为目标对象架构中的主键字段。
- **必填？** - 是否需要在目标应用程序或系统中填充该属性。
- **多值？** - 属性是否支持多个值。
- **区分大小写?** - 是否以区分大小写的方式计算属性值。
- **API 表达式**- 除非特定预配连接器的文档（如工作日）的文档指示不要使用。
- **引用对象属性**- 如果它是参考类型属性，则此菜单允许您在目标应用程序中选择包含与该属性关联的值的表和属性。 例如，如果名为“Department”的属性的存储值引用了独立“Departments”表中的对象，则需要选择“Departments.Name”。 给定应用程序支持的引用表和主 ID 字段已预先配置，当前无法使用 Azure 门户进行编辑，但可以使用[Microsoft 图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)进行编辑。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>将自定义扩展属性预配到符合 SCIM 的应用程序
SCIM RFC 定义了核心用户和组架构，同时还允许扩展架构以满足应用程序的需求。 要向 SCIM 应用程序添加自定义属性，请执行：
   1. 登录到 Azure[活动目录门户](https://aad.portal.azure.com)，选择**企业应用程序**，选择应用程序，然后选择**预配**。
   2. 在 **"映射"** 下，选择要为其添加自定义属性的对象（用户或组）。
   3. 在页面底部，选择 **"显示高级选项**"。
   4. 为**应用名称选择"编辑属性列表**"。
   5. 在属性列表的底部，在提供的字段中输入有关自定义属性的信息。 然后选择 **"添加属性**"。

对于 SCIM 应用程序，属性名称必须遵循下面示例中所示的模式。 "自定义扩展名称"和"自定义属性"可以根据您的应用程序要求进行自定义，例如：urn：ietf：参数：scim：架构：扩展：2.0：自定义扩展名称：自定义属性或 urn：ietf：参数：scim：架构：扩展：自定义扩展名称：2.0：用户.自定义属性名称：值

这些说明仅适用于支持 SCIM 的应用程序。 服务现在和 Salesforce 等应用程序不使用 SCIM 与 Azure AD 集成，因此在添加自定义属性时不需要此特定的命名空间。

自定义属性不能是引用属性或多值属性。 自定义多值扩展属性目前仅支持库中的应用程序。  
 
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
使用以下步骤为应用程序预配用户的角色。 请注意，以下说明特定于自定义 SCIM 应用程序。 对于库应用程序（如 Salesforce 和服务Now），请使用预定义的角色映射。 下面的项目符号描述如何将 AppRole 工作属性转换为应用程序期望的格式。

- 将 Azure AD 中的 AppRoleAssignment 映射到应用程序中的角色需要使用[表达式](../app-provisioning/functions-for-customizing-application-data.md)转换属性。 应用RoleAssignment属性**不应直接映射到**角色属性，而不使用表达式来分析角色详细信息。 

- **SingleAppRoleAssignment** 
  - **何时使用：** 使用 SingleAppRole 分配表达式为用户预配单个角色并指定主要角色。 
  - **如何配置：** 使用上述步骤导航到属性映射页，并使用 SingleAppRole 分配表达式映射到角色属性。 有三个角色属性可供选择：（角色[主要 eq"True"）显示、角色[主 eq"True"类型和角色[主 eq"True"\值）。 您可以选择在映射中包括任何或所有角色属性。 如果要包含多个映射，只需添加新映射并将其作为目标属性。  
  
  ![添加单应用程序角色分配](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **注意事项**
    - 确保未将多个角色分配给用户。 我们不能保证将预配哪个角色。
    
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
  
- **应用程序角色分配复杂** 
  - **何时使用：** 使用 AppRole 工作复杂表达式为用户预配多个角色。 
  - **如何配置：** 编辑上述受支持的属性列表，以包括角色的新属性： 
  
    ![添加角色](./media/customize-application-attributes/add-roles.png)<br>

    然后使用 AppRole工作复杂表达式映射到自定义角色属性，如下图所示：

    ![添加应用程序角色分配复杂](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **注意事项**
    - 所有角色都将预配为主角色 = false。
    - POST 包含角色类型。 PATCH 请求不包含类型。 我们正在努力在 POST 和 PATCH 请求中发送类型。
    
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
某些属性（如电话数字和电子邮件）是多值属性，您可能需要在其中指定不同类型的电话号码或电子邮件。 对多值属性使用下面的表达式。 它允许您指定属性类型并将其映射到该值的相应 Azure AD 用户属性。 

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

如果需要重新开始并将现有映射重置回其默认状态，可以选择"**还原默认映射**"复选框并保存配置。 这样做会设置所有映射和范围筛选器，就像应用程序刚刚从应用程序库中添加到 Azure AD 租户一样。

选择此选项将有效地强制在预配服务运行时重新同步所有用户。

> [!IMPORTANT]
> 在调用此选项之前，我们强烈建议将**预配状态**设置为 **"关闭**"。

## <a name="what-you-should-know"></a>要点

- Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。
- 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。
- 建议的最佳做法是至少将属性映射的连续更改数保持在最小。
- 现在不支持将要预配到应用的照片属性添加，因为您无法指定同步照片的格式。 您可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求该功能
- 属性 IsSoftDeleted 通常是应用程序的默认映射的一部分。 IsSoftdelete 可在四种情况之一中为 true（用户由于未从应用程序分配而不在范围范围内，用户由于不符合范围范围筛选器而退出范围，用户已在 Azure AD 中软删除，或者属性帐户启用设置为 false在用户上）。 
- Azure AD 预配服务不支持预配空值

## <a name="next-steps"></a>后续步骤

- [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
- [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)
- [用于用户预配的作用域筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](use-scim-to-provision-users-and-groups.md)
- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
