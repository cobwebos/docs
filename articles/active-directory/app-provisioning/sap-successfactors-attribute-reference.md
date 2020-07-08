---
title: SAP SuccessFactors 属性参考
description: 了解 SuccessFactors-HR 驱动的预配支持 SuccessFactors 中的哪些属性
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 12/06/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 25541b76dda55db1ec26f4d8e3ec63573a47e7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781542"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 属性参考

本文将介绍以下内容：

- [支持的 SuccessFactors 实体和属性](#supported-successfactors-entities-and-attributes)
- [默认属性映射](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>支持的 SuccessFactors 实体和属性

下表捕获了以下两个预配应用支持的 SuccessFactors 属性列表：

- [SuccessFactors Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors Azure AD 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)


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
| 10 | 用户                                   | addressLine1                 | 读取           |
| 11 | 用户                                   | addressLine2                 | 读取           |
| 12 | 用户                                   | addressLIne3                 | 读取           |
| 13 | 用户                                   | 商务电话                | 读取           |
| 14 | 用户                                   | 手机                    | 读取           |
| 15 | 用户                                   | city                         | 读取           |
| 16 | 用户                                   | country                      | 读取           |
| 17 | 用户                                   | custom01                     | 读取           |
| 18 | 用户                                   | custom02                     | 读取           |
| 19 | 用户                                   | custom03                     | 读取           |
| 20 | 用户                                   | custom04                     | 读取           |
| 21 | 用户                                   | custom05                     | 读取           |
| 22 | 用户                                   | custom06                     | 读取           |
| 23 | 用户                                   | custom07                     | 读取           |
| 24 | 用户                                   | custom08                     | 读取           |
| 25 | 用户                                   | custom09                     | 读取           |
| 26 | 用户                                   | custom10                     | 读取           |
| 27 | 用户                                   | custom11                     | 读取           |
| 28 | 用户                                   | custom12                     | 读取           |
| 29 | 用户                                   | custom13                     | 读取           |
| 30 | 用户                                   | custom14                     | 读取           |
| 31 | 用户                                   | empId                        | 读取           |
| 32 | 用户                                   | homePhone                    | 读取           |
| 33 | 用户                                   | jobFamily                    | 读取           |
| 34 | 用户                                   | nickname                     | 读取           |
| 35 | 用户                                   | state                        | 读取           |
| 36 | 用户                                   | timezone                     | 读取           |
| 37 | 用户                                   | username                     | 读取           |
| 38 | 用户                                   | zipCode                      | 读取           |
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
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | 读取           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | 读取           |
| 59 | EmpJob \. 公司                        | company                      | 读取           |
| 60 | EmpJob \. 公司                        | companyId                    | 读取           |
| 61 | EmpJob \. 公司 \. CountryOfRegistration | twoCharCountryCode           | 读取           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | 读取           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | 读取           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | 读取           |
| 65 | EmpJob \. 部门                     | department                   | 读取           |
| 66 | EmpJob \. 部门                     | departmentId                 | 读取           |
| 67 | EmpJob \. 除法                       | division                     | 读取           |
| 68 | EmpJob \. 除法                       | divisionId                   | 读取           |
| 69 | EmpJob \. JobCode                        | jobCode                      | 读取           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | 读取           |
| 71 | EmpJob \. 位置                       | LocationName                 | 读取           |
| 72 | EmpJob \. 位置                       | officeLocationAddress        | 读取           |
| 73 | EmpJob \. 位置                       | officeLocationCity           | 读取           |
| 74 | EmpJob \. 位置                       | officeLocationCustomString4  | 读取           |
| 75 | EmpJob \. 位置                       | officeLocationZipCode        | 读取           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | 读取           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 读取           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 读取           |

## <a name="default-attribute-mapping"></a>默认属性映射

下表提供了上面列出的 SuccessFactors 特性与 AD/Azure AD 特性之间的默认属性映射。 在 Azure AD 预配应用 "映射" 边栏选项卡中，可以修改此默认映射，使其包含上述列表中的属性。 

| \# | SuccessFactors 实体                  | SuccessFactors 特性 | 默认 AD/Azure AD 属性映射   | 处理注释                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 用作匹配属性                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[未映射 \- 为源锚点\] | 初始同步期间，预配服务会将 personUuid 链接到现有 objectGuid\。  |
| 3  | PerPersonal                            | displayName              | displayName                             | 不可用                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | 不可用                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | 不可用                                                                                           |
| 6  | 用户                                   | addressLine1             | streetAddress                           | 不可用                                                                                           |
| 7  | 用户                                   | city                     | l                                       | 不可用                                                                                           |
| 8  | 用户                                   | country                  | co                                      | 不可用                                                                                           |
| 9  | 用户                                   | state                    | st                                      | 不可用                                                                                           |
| 10 | 用户                                   | username                 | samAccountName                          | 不可用                                                                                           |
| 11 | 用户                                   | zipCode                  | postalCode                              | 不可用                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | 不可用                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | 不可用                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | 不可用                                                                                           |
| 15 | EmpJob \. 公司 \. CountryOfRegistration | twoCharCountryCode       | c                                       | 不可用                                                                                           |
| 16 | EmpJob \. 部门                     | department               | department                              | 不可用                                                                                           |
| 17 | EmpJob \. 除法                       | division                 | company                                 | 不可用                                                                                           |
| 18 | EmpJob \. 位置                       | officeLocationAddress    | streetAddress                           | 不可用                                                                                           |
| 19 | EmpJob \. 位置                       | officeLocationZipCode    | postalCode                              | 不可用                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | 如果 activeEmploymentsCount = 0，则禁用 account\。                                           |
