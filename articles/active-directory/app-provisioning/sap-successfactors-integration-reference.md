---
title: Azure Active Directory 和 SAP SuccessFactors 集成参考
description: 深入了解 SAP SuccessFactors-HR 驱动预配
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 805cdc0713afd43502bb224cce60167adbc418ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969521"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory 预配如何与 SAP SuccessFactors 集成 

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md) 与 [SAP SuccessFactors 员工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 集成，以管理用户的标识生命周期。 Azure Active Directory 提供三个预先构建的集成： 

* [SuccessFactors 到本地 Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors Azure Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors 写回](../saas-apps/sap-successfactors-writeback-tutorial.md)

本文介绍集成的工作原理，以及如何为不同的 HR 方案自定义设置行为。 

## <a name="establishing-connectivity"></a>建立连接 
Azure AD 预配服务使用基本身份验证连接到员工中心 OData API 终结点。 设置 SuccessFactors 预配应用时，请使用 "*管理员凭据*" 部分中的 "*租户 URL* " 参数来配置[API 数据中心 URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682)。 

若要进一步保护 Azure AD 预配服务和 SuccessFactors 之间的连接，可以使用下面所述的步骤，在 SuccessFactors IP 允许列表中添加 Azure AD IP 范围：

1. 下载 Azure 公有云的[最新 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519) 
1. 打开该文件并搜索标记 **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 范围](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 复制元素 *addressPrefixes* 中列出的所有 IP 地址范围，并使用范围生成 IP 地址限制列表。
1. 将 CIDR 值转换为 IP 范围。  
1. 登录到 SuccessFactors 管理门户，将 IP 范围添加到允许列表。 请参阅 SAP [支持说明 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)。 你现在可以在此工具中 [输入 IP 范围](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) 。 

## <a name="supported-entities"></a>支持的实体
对于 SuccessFactors 中的每个用户，Azure AD 预配服务将检索以下实体。 使用 OData API *$expand* 查询参数展开每个实体。 请参阅下面的 *检索规则* 列。 默认情况下，将展开某些实体，而某些实体仅在映射中存在特定属性时才会展开。 

| \# | SuccessFactors 实体                  | OData 节点     | 检索规则 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *根节点*                  | 始终           |
| 2  | PerPersonal                            | personalInfoNav              | 始终           |
| 3  | PerPhone                               | phoneNav                     | 始终           |
| 4  | PerEmail                               | emailNav                     | 始终           |
| 5  | EmpEmployment                          | employmentNav                | 始终           |
| 6  | 用户                                   | employmentNav/userNav        | 始终           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | 始终           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | 始终           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | 仅当 `company` `companyId` 映射或特性时 |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | 仅当 `department` `departmentId` 映射或特性时 |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | 仅当 `businessUnit` `businessUnitId` 映射或特性时 |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | 仅当 `costCenter` `costCenterId` 映射或特性时 |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | 仅当 `division` `divisionId` 映射或特性时 |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | 仅当 `jobCode` `jobCodeId` 映射或特性时 |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | 仅当 `payGrade` 映射属性时 |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | 仅当 `location` 映射属性时 |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | 如果映射包含以下属性之一： `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | 仅当 `eventReason` 映射属性时 |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | 仅当 `assignmentType` 映射时 |
| 20 | EmploymentType 选择列表                | employmentNav/jobInfoNav/employmentTypeNav | 仅当 `employmentType` 映射时 |
| 21 | EmployeeClass 选择列表                 | employmentNav/jobInfoNav/employeeClassNav | 仅当 `employeeClass` 映射时 |
| 22 | EmplStatus 选择列表                    | employmentNav/jobInfoNav/emplStatusNav | 仅当 `emplStatus` 映射时 |
| 23 | AssignmentType 选择列表                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | 仅当 `assignmentType` 映射时 |

## <a name="how-full-sync-works"></a>完全同步的工作原理
根据属性映射，在完全同步期间 Azure AD 预配服务将发送以下 "GET" OData API 查询，以提取所有活动用户的有效数据。 

> [!div class="mx-tdCol2BreakAll"]
>| 参数 | 说明 |
>| ----------|-------------|
>| OData API 主机 | 将 https 追加到 *租户 URL*。 示例：`https://api4.successfactors.com` |
>| OData API 终结点 | `/odata/v2/PerPerson` |
>| OData $format 查询参数 | `json` |
>| OData $filter 查询参数 | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand 查询参数 | 此参数值取决于所映射的属性。 示例：`employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize 查询参数 | `100` |

> [!NOTE]
> 在第一个初始完全同步期间，Azure AD 预配服务不会提取非活动/终止的工作线程数据。

对于每个 SuccessFactors 用户，预配服务将在目标 (Azure AD/本地 Active Directory) 使用映射中定义的匹配属性来查找帐户。 例如：如果 *personIdExternal* 映射到 *雇员 id* 并设置为匹配属性，则预配服务将使用 *PersonIdExternal* 值搜索具有 *雇员 id* 筛选器的用户。 如果找到用户匹配项，则会更新目标特性。 如果未找到匹配项，则它会在目标中创建一个新项。 

若要验证 OData API 终结点针对特定的数据返回的数据 `personIdExternal` ，请 `SuccessFactorsAPIEndpoint` 使用 api 数据中心服务器 URL 更新以下 api 查询中的，并使用 [Postman](https://www.postman.com/downloads/) 之类的工具调用查询。 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>增量同步的工作原理

完全同步后，Azure AD 预配服务 `LastExecutionTimestamp` 将保留并使用它来创建增量查询来检索增量更改。 计算每个 SuccessFactors 实体（例如、、和）中存在的时间戳属性， `lastModifiedDateTime` `startDate` `endDate` `latestTerminationDate` 以确定更改是否在 `LastExecutionTimestamp` 和之间 `CurrentExecutionTime` 。 如果是，则会将项更改视为有效并处理同步。 

## <a name="reading-attribute-data"></a>读取属性数据

Azure AD 预配服务查询 SuccessFactors 时，它将检索 JSON 结果集。 JSON 结果集包含存储在员工中心的多个属性。 默认情况下，设置架构配置为仅检索那些属性的子集。 

若要检索其他属性，请执行下列步骤：
    
1. 浏览到 "**企业应用程序**" "  ->  **SuccessFactors 应用**  ->  **设置**" "  ->  **编辑预配**  ->  **属性-映射" 页**。
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 " **编辑 SuccessFactors 的属性列表**"。 

   > [!NOTE] 
   > 如果 Azure 门户中未显示 " **编辑属性的属性列表** " 选项，请使用 URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 来访问该页面。 

1. 此视图中的 " **API 表达式** " 列显示连接器使用的 JSONPath 表达式。

   >[!div class="mx-imgBorder"] 
   >![API 表达式](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 可以编辑现有的 JSONPath 值，也可以使用有效的 JSONPath 表达式向架构中添加新的属性。 

下一部分提供了用于编辑 JSONPath 值的常见方案列表。 

## <a name="handling-different-hr-scenarios"></a>处理不同的 HR 方案

JSONPath 是适用于 JSON 的一种查询语言，类似于 XML 的 XPath。 与 XPath 类似，JSONPath 允许从 JSON 有效负载中提取和筛选数据。

通过使用 JSONPath 转换，你可以自定义 Azure AD 预配应用的行为，以检索自定义属性并处理 rehire、辅助角色转换和全局分配等方案。 

本部分介绍如何为以下 HR 方案自定义预配应用： 
* [检索其他属性](#retrieving-additional-attributes)
* [检索自定义属性](#retrieving-custom-attributes)
* [处理工作线程转换方案](#handling-worker-conversion-scenario)
* [处理 rehire 方案](#handling-rehire-scenario)
* [处理全局分配方案](#handling-global-assignment-scenario)
* [处理并发作业方案](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>检索其他属性

默认 Azure AD SuccessFactors 预配应用架构附带了 [90 多个预定义的属性](sap-successfactors-attribute-reference.md)。 若要向预配架构添加更多 SuccessFactors 属性，请使用下列步骤： 

1. 使用以下 OData 查询从员工中心为有效测试用户检索数据。 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 确定与属性关联的员工中心实体
   * 如果该属性是 *EmpEmployment* 实体的一部分，请在 " *employmentNav* " 节点下查找该属性。 
   * 如果该属性是 *用户* 实体的一部分，请在 " *employmentNav/userNav* " 节点下查找该属性。
   * 如果该属性是 *EmpJob* 实体的一部分，请在 *employmentNav/jobInfoNav* 节点下查找该属性。 
1. 构造与属性关联的 JSON 路径，并将此新属性添加到 SuccessFactors 属性的列表中。 
   * 示例1：假设要添加属性 *okToRehire*，它是 *employmentNav* 实体的一部分，然后使用 JSONPath  `$.employmentNav.results[0].okToRehire`
   * 示例2：假设要添加属性 *时区*，这是 *userNav* 实体的一部分，然后使用 JSONPath `$.employmentNav.results[0].userNav.timeZone`
   * 示例3：假设要添加属性 *flsaStatus*，它是 *jobInfoNav* 实体的一部分，然后使用 JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. 保存该架构。 
1. 重新开始预配。

### <a name="retrieving-custom-attributes"></a>检索自定义属性

默认情况下，以下自定义属性在 Azure AD SuccessFactors 预配应用中预定义： 
* *custom01-* 来自用户 (userNav) 实体的 custom15
* *customString1-customString15* from EmpEmployment (employmentNav) 实体称为 *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* from EmpJobInfo (jobInfoNav) 实体称为 *empJobNavCustomString1-empNavJobCustomString15*

假设你的员工中心实例中的 *customString35* *属性存储位置* 说明。 需要将此值流向 Active Directory *physicalDeliveryOfficeName* 属性。 若要为此方案配置属性映射，请使用下面给出的步骤： 

1. 编辑 SuccessFactors 属性列表以添加名为 *empJobNavCustomString35*的新属性。
1. 为此属性设置 JSONPath API 表达式，如下所示： `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. 保存并重新加载 Azure 门户中的映射更改。  
1. 在 "属性映射" 边栏选项卡中，将 *empJobNavCustomString35* 映射到 *physicalDeliveryOfficeName*。
1. 保存映射。

扩展此方案： 
* 如果要从*用户*实体映射*custom35*属性，请使用 JSONPath`$.employmentNav.results[0].userNav.custom35`
* 如果要从*EmpEmployment*实体映射*customString35*属性，请使用 JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>处理工作线程转换方案

工作人员转换是指将现有全职员工及时转换为承包商或合同工的过程。 在此方案中，员工中心为同一*人员*实体添加新的*EmpEmployment*实体和新*用户*实体。 在上一个*EmpEmployment*实体下嵌套的*用户*实体设置为 null。 若要处理这种情况，以便在进行转换时显示新的雇用数据，可以使用下面列出的步骤批量更新预配应用架构：  

1. 打开 SuccessFactors 预配应用程序的 "属性映射" 边栏选项卡。 
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 "在 **此处查看你的架构** " 链接以打开 "架构编辑器"。 

   >![屏幕截图显示 "在此处查看架构" 链接，该链接可打开架构编辑器。](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. 单击 " **下载** " 链接，在编辑之前保存架构的副本。 

   >![屏幕截图显示具有下载选择的架构编辑器，以保存架构的副本。](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. 在架构编辑器中，按 Ctrl + H 键打开 "查找-替换" 控件。
1. 在 "查找" 文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在 "替换" 文本框中，复制并粘贴值 `$.employmentNav.results[?(@.userNav != null)]` 。 请注意运算符周围的空白 `!=` ，这对于成功处理 JSONPath 表达式非常重要。 
   >![查找-替换-转换](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. 单击 "全部替换" 选项以更新架构。 
1. 保存该架构。 
1. 上述过程将更新所有 JSONPath 表达式，如下所示： 
   * 旧 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新开始预配。 

### <a name="handling-rehire-scenario"></a>处理 rehire 方案

通常有两个选项可用于处理 rehires： 
* 选项1：在员工中心创建新的人员配置文件
* 选项2：在员工中心重复使用现有人员配置文件

如果 HR 进程使用选项1，则不需要对设置架构进行任何更改。 如果你的 HR 进程使用选项2，则 Employee Central 会为同一*Person*实体添加一个新的*EmpEmployment*实体和一个新的*用户*实体。 与转换方案不同，上一个*EmpEmployment*实体中的*用户*实体未设置为 null。 

若要处理此 rehire 方案 (选项 2) ，以便为 rehire 配置文件显示最新的雇用数据，可以使用下面列出的步骤批量更新预配应用架构：  

1. 打开 SuccessFactors 预配应用程序的 "属性映射" 边栏选项卡。 
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 "在 **此处查看你的架构** " 链接以打开 "架构编辑器"。   
1. 单击 " **下载** " 链接，在编辑之前保存架构的副本。   
1. 在架构编辑器中，按 Ctrl + H 键打开 "查找-替换" 控件。
1. 在 "查找" 文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在 "替换" 文本框中，复制并粘贴值 `$.employmentNav.results[-1:]` 。 此 JSONPath 表达式返回最新的 *EmpEmployment* 记录。   
1. 单击 "全部替换" 选项以更新架构。 
1. 保存该架构。 
1. 上述过程将更新所有 JSONPath 表达式，如下所示： 
   * 旧 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新开始预配。 

此架构更改还支持辅助角色转换方案。 

### <a name="handling-global-assignment-scenario"></a>处理全局分配方案

为全局分配处理 Employee Central 中的用户时，SuccessFactors 将添加新的 *EmpEmployment* 实体并将 *assignmentClass* 设置为 "GA"。 它还会创建新的 *用户* 实体。 因此，用户现在具有：
* 对应于 home 赋值的一个*EmpEmployment*  +  *用户*实体， *assignmentClass*设置为 "ST" 和 
* 与*EmpEmployment*  +  *assignmentClass*设置为 "GA" 的全局分配相对应的另一个 EmpEmployment*用户*实体

若要提取属于标准分配和全局分配用户配置文件的属性，请使用下列步骤： 

1. 打开 SuccessFactors 预配应用程序的 "属性映射" 边栏选项卡。 
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 "在 **此处查看你的架构** " 链接以打开 "架构编辑器"。   
1. 单击 " **下载** " 链接，在编辑之前保存架构的副本。   
1. 在架构编辑器中，按 Ctrl + H 键打开 "查找-替换" 控件。
1. 在 "查找" 文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在 "替换" 文本框中，复制并粘贴值 `$.employmentNav.results[?(@.assignmentClass == 'ST')]` 。 
1. 单击 "全部替换" 选项以更新架构。 
1. 保存该架构。 
1. 上述过程将更新所有 JSONPath 表达式，如下所示： 
   * 旧 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. 重载应用的 "属性映射" 边栏选项卡。 
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 " **编辑 SuccessFactors 的属性列表**"。
1. 添加新属性以提取全局分配数据。 例如：若要提取与全局分配配置文件关联的部门名称，可以添加属性 *globalAssignmentDepartment* ，并将 JSONPath 表达式设置为 `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` 。 
1. 你现在可以将两个部门值都流动到 Active Directory 属性，或使用表达式映射有选择地流式传输值。 示例：下面的表达式将 AD *部门* 属性的值设置为 *globalAssignmentDepartment* （如果存在），否则它会将值设置为与标准分配关联的 *部门* 。 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. 保存映射。 
1. 重新开始预配。 

### <a name="handling-concurrent-jobs-scenario"></a>处理并发作业方案

员工中心中的用户具有并发/多个作业时，有两个 *EmpEmployment* 和 *用户* 实体，其中 *assignmentClass* 设置为 "ST"。 若要提取属于这两个作业的属性，请使用下面列出的步骤： 

1. 打开 SuccessFactors 预配应用程序的 "属性映射" 边栏选项卡。 
1. 向下滚动，然后单击 " **显示高级选项**"。
1. 单击 " **编辑 SuccessFactors 的属性列表**"。
1. 假设你想要拉取与作业1和作业2关联的部门。 预定义的属性 *系* 已经获取第一个作业的部门值。 您可以定义一个名为 *secondJobDepartment* 的新属性，并将 JSONPath 表达式设置为 `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. 你现在可以将两个部门值都流动到 Active Directory 属性，或使用表达式映射有选择地流式传输值。 
1. 保存映射。 
1. 重新开始预配。 

## <a name="writeback-scenarios"></a>写回方案

本部分介绍了不同的写回方案。 它根据在 SuccessFactors 中设置电子邮件和电话号码的方式推荐配置方法。

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>电话和电子邮件写回支持的方案 

| \# | 方案要求 | 电子邮件主 <br> 标志值 | 工作电话 <br> 主标志值 | 手机 <br> 主标志值 | 工作电话 <br> mapping | 手机 <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * 仅将业务电子邮件设置为主电子邮件。 <br> * 请勿设置电话号码。 | true | true | false | \[未设置\] | \[未设置\] | 
| 2 | * 在 SuccessFactors 中，业务电子邮件和商务电话是主要的 <br> * 始终 Azure AD 电话号码传递到 business phone，并移动到手机。 | true | true | false | telephoneNumber | mobile | 
| 3 | * 在 SuccessFactors 中，业务电子邮件和手机是主要的 <br> * 始终 Azure AD 电话号码传递到业务电话并移动到手机 | true | false | true |  telephoneNumber | mobile | 
| 4 | * 在 SuccessFactors 中，业务电子邮件是主电子邮件 <br> * 在 Azure AD 中，检查是否存在工作电话号码（如果存在），然后检查是否还存在 "移动号码"，将 "工作电话号码" 标记为 "仅当移动电话号码不存在时"。 | true | 使用表达式映射： `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 使用表达式映射： `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * 在 SuccessFactors business email 和 business phone 中是主要的。 <br> * 在 Azure AD 中，如果移动可用，请将其设置为 business phone，否则请使用 telephoneNumber。 | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[未设置\] | 

* 如果写回属性映射中没有电话号码的映射，则写回后仅包含电子邮件。
* 在员工中心加入新员工时，企业电子邮件和电话号码可能不可用。 如果在载入期间将业务电子邮件和商务电话设置为 "主要" 是必需的，则可以在创建新员工期间为企业电话和电子邮件设置一个虚拟值，这将最终由写回应用程序进行更新。
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>不受支持的电话和电子邮件写回方案

* 在员工中心，在加入的个人电子邮件和个人电话设置为 "主要"。 写回应用程序无法切换此设置，并将业务电子邮件和商务电话设置为主要。
* 在员工中心，"商务电话" 设置为 "主要"。 写回应用程序无法更改此设置，也无法将手机设置为主要。
* 写回应用程序无法读取当前的主标志设置，也不能为写入操作使用相同的值。 将始终使用属性映射中配置的标志值。 

## <a name="next-steps"></a>后续步骤

* [了解如何配置 SuccessFactors 以 Active Directory 预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [了解如何配置写回 SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [详细了解用于入站预配的支持 SuccessFactors 属性](sap-successfactors-attribute-reference.md)










