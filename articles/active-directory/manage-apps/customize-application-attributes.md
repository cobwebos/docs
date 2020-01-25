---
title: 自定义 Azure AD 属性映射 |Microsoft Docs
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
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
ms.openlocfilehash: d21ebabb34b828624c196922f88380f02234dc05
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711862"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射

Microsoft Azure AD 为第三方 SaaS 应用程序（如 Salesforce、G Suite 等）提供对用户预配的支持。 如果为第三方 SaaS 应用程序启用用户预配，则 Azure 门户通过属性映射控制其属性值。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 某些应用管理其他类型的对象，以及用户（如组）。

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 因此，你可以更改或删除现有的属性映射，或者创建新的属性映射。

## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

请按照以下步骤访问用户预配的**映射**功能：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用程序以加载其应用管理窗格，您可以在其中查看报告和管理应用设置。
1. 选择 "**设置**" 以管理所选应用的用户帐户预配设置。
1. 展开 "**映射**"，以查看和编辑在 Azure AD 和目标应用程序之间流动的用户属性。 如果目标应用程序支持它，则可以选择配置组和用户帐户的预配。

   ![使用映射查看和编辑用户属性](media/customize-application-attributes/21.png)

1. 选择**映射**配置以打开相关的**属性映射**屏幕。 要使 SaaS 应用程序正常运行，需要一些属性映射。 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.

   ![使用属性映射来配置应用的属性映射](media/customize-application-attributes/22.png)

   在此屏幕截图中，可以看到，Salesforce 中托管对象的**Username**属性用链接的 Azure Active Directory 对象的**userPrincipalName**值填充。

1. 选择现有的**属性映射**，以打开 "**编辑属性**" 屏幕。 在此处可以编辑在 Azure AD 和目标应用程序之间流动的用户属性。

   ![使用编辑属性编辑用户属性](media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해

특성 매핑으로 타사 SaaS 애플리케이션에서 특성이 채워지는 법을 제어합니다.
4 가지의 다른 매핑 형식이 지원됩니다.

- **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
- **常量**-目标属性使用指定的特定字符串填充。
- **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다.
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)을 참조하세요.
- **None** - 대상 특성이 수정되지 않고 남아있습니다. 但是，如果目标属性曾经为空，则会用您指定的默认值进行填充。

除了这四种基本类型外，自定义属性映射还支持可选**默认**值赋值的概念。 如果在 Azure AD 或目标对象上没有值，则默认值分配将确保用值填充目标属性。 이 값을 비워두는 것이 가장 일반적인 구성입니다.

### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

在上一节中，已将你引入 "属性映射类型" 属性。
除了此属性，属性映射还支持以下属性：

- **원본 특성** - 원본 시스템의 사용자 특성(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성(예: ServiceNow).
- **默认值（可选）** -如果源属性为 null，则将传递给目标系统的值。 只有在创建用户时才会设置此值。 更新现有用户时，不会设置 "默认值 when null"。 例如，如果你想要使用特定职务（当它在源系统中为 null 时）来预配目标系统中的所有现有用户，则可以使用以下[表达式](functions-for-customizing-application-data.md)： Switch （IsPresent （[jobTitle]）、"DefaultValue"、"True"、[jobTitle]）。 请确保将 "默认值" 替换为源系统中的 null 值时要设置的值。 
- **使用此属性匹配对象**–是否应使用此映射唯一标识源系统和目标系统之间的用户。 它通常在 Azure AD 中的 userPrincipalName 或 mail 属性上设置，该属性通常映射到目标应用程序中的用户名字段。
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 如果有多个，则按此字段定义的顺序对它们进行评估。 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
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

## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

选定数量的应用程序（如 ServiceNow、Box 和 G Suite）支持预配组对象和用户对象。 组对象可以包含组属性（如显示名称和电子邮件别名）以及组成员。

![示例显示具有预配组和用户对象的 ServiceNow](media/customize-application-attributes/24.png)

可以选择启用或禁用组设置，方法是在 "**映射**" 下选择组映射，并在 "**属性映射**" 屏幕中选择 "**启用**" 选项。

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

> [!TIP]
> 그룹 개체(속성 및 멤버) 프로비전은 애플리케이션에 [그룹을 할당](assign-user-or-group-access-portal.md)하는 것과 별개의 개념입니다. 애플리케이션에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.

## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 애플리케이션에 지원되는 사용자 특성은 미리 구성됩니다. 大多数应用程序的用户管理 Api 不支持架构发现。 因此，Azure AD 预配服务无法通过调用应用程序来动态生成受支持的属性列表。

但是，某些应用程序支持自定义属性，Azure AD 预配服务可以读取和写入自定义属性。 若要将其定义输入到 Azure 门户中，请选中 "**属性映射**" 屏幕底部的 "**显示高级选项**" 复选框，然后选择 "编辑应用的**属性列表**"。

특성 목록의 사용자 지정을 지원하는 애플리케이션 및 시스템은 다음과 같습니다.

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory([Azure AD Graph API 기본 특성](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) 및 사용자 지정 디렉터리 확장이 지원됩니다.)
- [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

> [!NOTE]
> 애플리케이션과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 애플리케이션이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다.

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

- **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다.
- **类型**-属性存储的数据的类型，在目标对象的架构中定义，可以是以下类型之一：
  - *이진* - 특성에 이진 데이터가 포함됩니다.
  - *부울* - 특성에 True 또는 False 값이 포함됩니다.
  - *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
  - *정수* - 특성에 정수가 포함됩니다.
  - *참조* - 특성에 대상 애플리케이션의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
  - *문자열* - 특성에 텍스트 문자열이 포함됩니다.
- **기본 키 여부** -特性是否定义为目标对象的架构中的主键字段。
- **필수 여부** -是否需要在目标应用程序或系统中填充属性。
- **다중 값 여부** -特性是否支持多个值。
- **대소문자 구분 여부** -是否以区分大小写的方式对属性值进行求值。
- **API 表达式**-不使用，除非通过特定预配连接器（如 Workday）的文档的指示执行此操作。
- **被引用的对象特性**-如果它是引用类型属性，则可以使用此菜单选择包含与属性关联的值的目标应用程序中的表和属性。 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 给定应用程序支持的引用表和主要 ID 字段是预先配置的，目前无法使用 Azure 门户进行编辑，但可以使用[图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)进行编辑。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>将自定义扩展属性预配到符合 SCIM 的应用程序
SCIM RFC 定义核心用户和组架构，同时允许对架构进行扩展，以满足应用程序的需求。 向 SCIM 应用程序添加自定义属性：
   1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)，选择 "**企业应用程序**"，选择应用程序，然后选择 "**设置**"。
   2. 在 "**映射**" 下，选择要为其添加自定义属性的对象（用户或组）。
   3. 在页面底部，选择 "**显示高级选项**"。
   4. 选择 "**编辑 AppName 的属性列表**"。
   5. 在属性列表的底部，在提供的字段中输入自定义属性的相关信息。 然后选择 "**添加属性**"。

对于 SCIM 应用程序，属性名称必须遵循以下示例中所示的模式。 可以根据应用程序的要求自定义 "CustomExtensionName" 和 "CustomAttribute"，例如： urn： ietf： params： scim：架构：扩展：2.0： CustomExtensionName： CustomAttribute

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

- 将 Azure AD 中的 appRoleAssignment 映射到应用程序中的某个角色需要使用[表达式](functions-for-customizing-application-data.md)来转换该特性。 **不应将 appRoleAssignment 特性直接映射**到 role 特性，而无需使用表达式来分析角色的详细信息。 

- **SingleAppRoleAssignment** 
  - **何时使用：** 使用 SingleAppRoleAssignment 表达式为用户设置单个角色并指定主角色。 
  - **如何配置：** 使用上述步骤导航到 "属性映射" 页，并使用 SingleAppRoleAssignment 表达式映射到 roles 属性。 有三个角色属性可供选择：（角色 [主 eq "True"]。显示、角色 [主 eq "True"）、类型和角色 [主 eq "True"]。 你可以选择在映射中包含任何或所有角色属性。 如果要包含多个映射，只需添加一个新映射，并将其包含为目标属性即可。  
  
  ![添加 SingleAppRoleAssignment](media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **要考虑的事项**
    - 确保未向用户分配多个角色。 我们无法保证将预配哪个角色。
    
  - **예제 출력** 

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
  
    ![역할 추가](media/customize-application-attributes/add-roles.png)<br>

    然后，使用 AppRoleAssignmentsComplex 表达式映射到自定义角色属性，如下图所示：

    ![添加 AppRoleAssignmentsComplex](media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **要考虑的事项**
    - 所有角色都将设置为主 = false。
    - POST 包含角色类型。 修补请求不包含类型。 我们正在努力发送 POST 和 PATCH 请求中的类型。
    
  - **예제 출력** 
  
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

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>기본 특성 및 특성 매핑 복원

如果需要重新启动现有映射并将其重置回默认状态，可以选中 "**还原默认映射**" 复选框并保存配置。 这样做会设置所有映射，就像应用程序库中刚添加到 Azure AD 租户的应用程序一样。

如果选择此选项，则在预配服务正在运行时，会有效地强制执行所有用户的重新同步。

> [!IMPORTANT]
> 在调用此选项之前，强烈建议将**预配状态**设置为 "**关闭**"。

## <a name="what-you-should-know"></a>알아야 할 사항

- Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다.
- 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다.
- 建议的最佳做法是将连续更改的次数保持在最少的属性映射。
- 由于不能指定用于同步照片的格式，因此目前不支持将预配照片属性添加到应用。 可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求该功能
- 特性 IsSoftDeleted 通常是应用程序的默认映射的一部分。 在以下四种情况中的一种情况下，IsSoftdeleted 可能为 true （用户因未分配到应用程序而超出范围，用户由于未满足范围筛选器而超出范围，用户已在 Azure AD 中软删除，或属性 AccountEnabled 设置为 false 在用户上）。 
- Azure AD 预配服务不支持预配 null 值

## <a name="next-steps"></a>다음 단계

- [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
- [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
