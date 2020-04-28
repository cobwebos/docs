---
title: SAP SuccessFactors 属性参考 |Microsoft Docs
description: 了解 SuccessFactors 中的哪些属性受 SuccessFactors 驱动的预配支持
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522350"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 属性参考

## <a name="supported-successfactors-entities-and-attributes"></a>支持的 SuccessFactors 实体和属性

下表捕获了以下两个预配应用支持的 SuccessFactors 属性列表： 
* [SuccessFactors Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors Azure AD 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors 实体                  | SuccessFactors 特性     | 操作类型 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | 读取           |
| 2  | PerPerson                              | personId                     | 读取           |
| 3  | PerPerson                              | perPersonUuid                | 读取           |
| 4  | PerPersonal                            | displayName                  | 读取           |
| 5  | PerPersonal                            | firstName                    | 读取           |
| 6  | PerPersonal                            | gender                       | 读取           |
| 7  | PerPersonal                            | lastName                     | 读取           |
| 8  | PerPersonal                            | middleName                   | 读取           |
| 9  | PerPersonal                            | preferredName                | 读取           |
| 10 | User                                   | addressLine1                 | 读取           |
| 11 | User                                   | addressLine2                 | 读取           |
| 12 | User                                   | addressLIne3                 | 读取           |
| 13 | User                                   | 商务电话                | 读取           |
| 14 | User                                   | 手机                    | 读取           |
| 15 | User                                   | city                         | 读取           |
| 16 | User                                   | country                      | 读取           |
| 17 | User                                   | custom01                     | 读取           |
| 18 | User                                   | custom02                     | 读取           |
| 19 | User                                   | custom03                     | 读取           |
| 20 | User                                   | custom04                     | 读取           |
| 21 | User                                   | custom05                     | 读取           |
| 22 | User                                   | custom06                     | 读取           |
| 23 | User                                   | custom07                     | 读取           |
| 24 | User                                   | custom08                     | 读取           |
| 25 | User                                   | custom09                     | 读取           |
| 26 | User                                   | custom10                     | 读取           |
| 27 | User                                   | custom11                     | 读取           |
| 28 | User                                   | custom12                     | 读取           |
| 29 | User                                   | custom13                     | 读取           |
| 30 | User                                   | custom14                     | 读取           |
| 31 | User                                   | empId                        | 读取           |
| 32 | User                                   | homePhone                    | 读取           |
| 33 | User                                   | jobFamily                    | 读取           |
| 34 | User                                   | nickname                     | 读取           |
| 35 | User                                   | state                        | 读取           |
| 36 | User                                   | timezone                     | 读取           |
| 37 | User                                   | username                     | 读取           |
| 38 | User                                   | zipCode                      | 读取           |
| 39 | PerPhone                               | 区号                     | 读取           |
| 40 | PerPhone                               | countryCode                  | 读取           |
| 41 | PerPhone                               | 扩展                    | 读取           |
| 42 | PerPhone                               | phoneNumber                  | 读取           |
| 43 | PerPhone                               | phoneType                    | 读取           |
| 44 | PerEmail                               | emailAddress                 | 读取和写入    |
| 45 | PerEmail                               | emailType                    | 读取           |
| 46 | EmpEmployment                          | firstDateWorked              | 读取           |
| 47 | EmpEmployment                          | lastDateWorked               | 读取           |
| 48 | EmpEmployment                          | userId                       | 读取           |
| 49 | EmpEmployment                          | isContingentWorker           | 读取           |
| 50 | EmpJob                                 | countryOfCompany             | 读取           |
| 51 | EmpJob                                 | emplStatus                   | 读取           |
| 52 | EmpJob                                 | endDate                      | 读取           |
| 53 | EmpJob                                 | startDate                    | 读取           |
| 54 | EmpJob                                 | jobTitle                     | 读取           |
| 55 | EmpJob                                 | position                     | 读取           |
| 65 | EmpJob                                 | customString13               | 读取           |
| 56 | EmpJob                                 | managerId                    | 读取           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | 读取           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | 读取           |
| 59 | EmpJob\.公司                        | company                      | 读取           |
| 60 | EmpJob\.公司                        | companyId                    | 读取           |
| 61 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode           | 读取           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | 读取           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | 读取           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | 读取           |
| 65 | EmpJob\.部门                     | department                   | 读取           |
| 66 | EmpJob\.部门                     | departmentId                 | 读取           |
| 67 | EmpJob\.除法                       | division                     | 读取           |
| 68 | EmpJob\.除法                       | divisionId                   | 读取           |
| 69 | EmpJob\.JobCode                        | jobCode                      | 读取           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | 读取           |
| 71 | EmpJob\.位置                       | LocationName                 | 读取           |
| 72 | EmpJob\.位置                       | officeLocationAddress        | 读取           |
| 73 | EmpJob\.位置                       | officeLocationCity           | 读取           |
| 74 | EmpJob\.位置                       | officeLocationCustomString4  | 读取           |
| 75 | EmpJob\.位置                       | officeLocationZipCode        | 读取           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | 读取           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 读取           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 读取           |


## <a name="default-attribute-mapping"></a>默认属性映射

下表提供了上面列出的 SuccessFactors 特性与 AD/Azure AD 特性之间的默认属性映射。 在 Azure AD 预配应用 "映射" 边栏选项卡中，可以修改此默认映射，使其包含上述列表中的属性。 

| \# | SuccessFactors 实体                  | SuccessFactors 特性 | 默认 AD/Azure AD 属性映射   | 处理注释                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 用作匹配属性                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[未映射\-为源锚点\] | 初始同步期间，预配服务会将 personUuid 链接到现有 objectGuid\。  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.部门                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob\.除法                       | division                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.位置                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.位置                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | 如果 activeEmploymentsCount = 0，则禁用 account\。                                           |

