---
title: SAP SuccessFactors 属性参考 |Microsoft Docs
description: 了解 SuccessFactors 中的哪些属性受 SuccessFactors 驱动的预配支持
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066182"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 属性参考

## <a name="supported-successfactors-entities-and-attributes"></a>支持的 SuccessFactors 实体和属性

下表捕获了以下两个预配应用支持的 SuccessFactors 属性列表： 
* [SuccessFactors Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors Azure AD 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors 实体                  | SuccessFactors 特性     | 操作类型 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | 读           |
| 2  | PerPerson                              | personId                     | 读           |
| 3  | PerPerson                              | perPersonUuid                | 读           |
| 4  | PerPersonal                            | displayName                  | 读           |
| 5  | PerPersonal                            | firstName                    | 读           |
| 6  | PerPersonal                            | gender                       | 读           |
| 7  | PerPersonal                            | lastName                     | 读           |
| 8  | PerPersonal                            | middleName                   | 读           |
| 9  | PerPersonal                            | preferredName                | 读           |
| 10 | 用户                                   | addressLine1                 | 读           |
| 11 | 用户                                   | addressLine2                 | 读           |
| 12 | 用户                                   | addressLIne3                 | 读           |
| 13 | 用户                                   | 商务电话                | 读           |
| 14 | 用户                                   | 手机                    | 读           |
| 15 | 用户                                   | 城市                         | 读           |
| 16 | 用户                                   | country                      | 读           |
| 17 | 用户                                   | custom01                     | 读           |
| 18 | 用户                                   | custom02                     | 读           |
| 19 | 用户                                   | custom03                     | 读           |
| 20 | 用户                                   | custom04                     | 读           |
| 21 | 用户                                   | custom05                     | 读           |
| 22 | 用户                                   | custom06                     | 读           |
| 23 | 用户                                   | custom07                     | 读           |
| 24 | 用户                                   | custom08                     | 读           |
| 25 | 用户                                   | custom09                     | 读           |
| 26 | 用户                                   | custom10                     | 读           |
| 27 | 用户                                   | custom11                     | 读           |
| 28 | 用户                                   | custom12                     | 读           |
| 29 | 用户                                   | custom13                     | 读           |
| 30 | 用户                                   | custom14                     | 读           |
| 31 | 用户                                   | empId                        | 读           |
| 32 | 用户                                   | homePhone                    | 读           |
| 33 | 用户                                   | jobFamily                    | 读           |
| 34 | 用户                                   | nickname                     | 读           |
| 35 | 用户                                   | state                        | 读           |
| 36 | 用户                                   | timezone                     | 读           |
| 37 | 用户                                   | username                     | 读           |
| 38 | 用户                                   | zipCode                      | 读           |
| 39 | PerPhone                               | 区号                     | 读           |
| 40 | PerPhone                               | countryCode                  | 读           |
| 41 | PerPhone                               | 扩展                    | 读           |
| 42 | PerPhone                               | phoneNumber                  | 读           |
| 43 | PerPhone                               | phoneType                    | 读           |
| 44 | PerEmail                               | emailAddress                 | 读取和写入    |
| 45 | PerEmail                               | emailType                    | 读           |
| 46 | EmpEmployment                          | firstDateWorked              | 读           |
| 47 | EmpEmployment                          | lastDateWorked               | 读           |
| 48 | EmpEmployment                          | userId                       | 读           |
| 49 | EmpEmployment                          | isContingentWorker           | 读           |
| 50 | EmpJob                                 | countryOfCompany             | 读           |
| 51 | EmpJob                                 | emplStatus                   | 读           |
| 52 | EmpJob                                 | endDate                      | 读           |
| 53 | EmpJob                                 | startDate                    | 读           |
| 54 | EmpJob                                 | jobTitle                     | 读           |
| 55 | EmpJob                                 | position                     | 读           |
| 65 | EmpJob                                 | customString13               | 读           |
| 56 | EmpJob                                 | managerId                    | 读           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | 读           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | 读           |
| 59 | EmpJob\.公司                        | company                      | 读           |
| 60 | EmpJob\.公司                        | companyId                    | 读           |
| 61 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode           | 读           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | 读           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | 读           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | 读           |
| 65 | EmpJob\.部门                     | department                   | 读           |
| 66 | EmpJob\.部门                     | departmentId                 | 读           |
| 67 | EmpJob\.除法                       | 除法                     | 读           |
| 68 | EmpJob\.除法                       | divisionId                   | 读           |
| 69 | EmpJob\.JobCode                        | jobCode                      | 读           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | 读           |
| 71 | EmpJob\.位置                       | LocationName                 | 读           |
| 72 | EmpJob\.位置                       | officeLocationAddress        | 读           |
| 73 | EmpJob\.位置                       | officeLocationCity           | 读           |
| 74 | EmpJob\.位置                       | officeLocationCustomString4  | 读           |
| 75 | EmpJob\.位置                       | officeLocationZipCode        | 读           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | 读           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 读           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 读           |


## <a name="default-attribute-mapping"></a>默认属性映射

下表提供了上面列出的 SuccessFactors 特性与 AD/Azure AD 特性之间的默认属性映射。 在 Azure AD 预配应用 "映射" 边栏选项卡中，可以修改此默认映射，使其包含上述列表中的属性。 

| \# | SuccessFactors 实体                  | SuccessFactors 特性 | 默认 AD/Azure AD 属性映射   | 处理注释                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 用作匹配属性                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[未映射为源锚点 \-\] | 初始同步期间，预配服务会将 personUuid 链接到现有 objectGuid\。  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | 用户                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | 用户                                   | 城市                     | l                                       | NA                                                                                           |
| 8  | 用户                                   | country                  | co                                      | NA                                                                                           |
| 9  | 用户                                   | state                    | st                                      | NA                                                                                           |
| 10 | 用户                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | 用户                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | 邮件                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.部门                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob\.除法                       | 除法                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.位置                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.位置                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | 如果 activeEmploymentsCount = 0，则禁用 account\。                                           |

