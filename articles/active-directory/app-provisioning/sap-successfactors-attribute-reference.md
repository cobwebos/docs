---
title: SAP 成功因素属性参考 |微软文档
description: 了解成功因素支持的成功因子的哪些属性-HR 驱动的预配
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522350"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP 成功因素属性参考

## <a name="supported-successfactors-entities-and-attributes"></a>支持成功因素实体和属性

下表捕获以下两个预配应用支持的 SuccessFactors 属性列表： 
* [活动目录用户预配的成功因素](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Azure AD 用户预配的成功因素](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | 成功因素实体                  | 成功因素属性     | 操作类型 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | 每人                              | 人 Id 外部             | 读取           |
| 2  | 每人                              | 人 Id                     | 读取           |
| 3  | 每人                              | 佩尔森乌伊德                | 读取           |
| 4  | 人均                            | displayName                  | 读取           |
| 5  | 人均                            | firstName                    | 读取           |
| 6  | 人均                            | gender                       | 读取           |
| 7  | 人均                            | lastName                     | 读取           |
| 8  | 人均                            | middleName                   | 读取           |
| 9  | 人均                            | 首选名称                | 读取           |
| 10 | 用户                                   | 地址Line1                 | 读取           |
| 11 | 用户                                   | 地址Line2                 | 读取           |
| 12 | 用户                                   | 地址LIne3                 | 读取           |
| 13 | 用户                                   | 商务电话                | 读取           |
| 14 | 用户                                   | 手机                    | 读取           |
| 15 | 用户                                   | city                         | 读取           |
| 16 | 用户                                   | country                      | 读取           |
| 17 | 用户                                   | 自定义01                     | 读取           |
| 18 | 用户                                   | 自定义02                     | 读取           |
| 19 | 用户                                   | 自定义03                     | 读取           |
| 20 | 用户                                   | 自定义04                     | 读取           |
| 21 | 用户                                   | 自定义05                     | 读取           |
| 22 | 用户                                   | 自定义06                     | 读取           |
| 23 | 用户                                   | 自定义07                     | 读取           |
| 24 | 用户                                   | 自定义08                     | 读取           |
| 25 | 用户                                   | 自定义09                     | 读取           |
| 26 | 用户                                   | 自定义10                     | 读取           |
| 27 | 用户                                   | 自定义11                     | 读取           |
| 28 | 用户                                   | 自定义12                     | 读取           |
| 29 | 用户                                   | 自定义13                     | 读取           |
| 30 | 用户                                   | 自定义14                     | 读取           |
| 31 | 用户                                   | empId                        | 读取           |
| 32 | 用户                                   | homePhone                    | 读取           |
| 33 | 用户                                   | 工作家庭                    | 读取           |
| 34 | 用户                                   | nickname                     | 读取           |
| 35 | 用户                                   | state                        | 读取           |
| 36 | 用户                                   | timezone                     | 读取           |
| 37 | 用户                                   | username                     | 读取           |
| 38 | 用户                                   | 邮政编码                      | 读取           |
| 39 | 每部电话                               | 区号                     | 读取           |
| 40 | 每部电话                               | countryCode                  | 读取           |
| 41 | 每部电话                               | 扩展                    | 读取           |
| 42 | 每部电话                               | phoneNumber                  | 读取           |
| 43 | 每部电话                               | 电话类型                    | 读取           |
| 44 | 每封电子邮件                               | emailAddress                 | 读取和写入    |
| 45 | 每封电子邮件                               | 电子邮件类型                    | 读取           |
| 46 | 恩普就业                          | 第一次工作日期              | 读取           |
| 47 | 恩普就业                          | 上次日期工作               | 读取           |
| 48 | 恩普就业                          | userId                       | 读取           |
| 49 | 恩普就业                          | 是临时工人           | 读取           |
| 50 | 恩普乔布                                 | 公司国家             | 读取           |
| 51 | 恩普乔布                                 | empl状态                   | 读取           |
| 52 | 恩普乔布                                 | endDate                      | 读取           |
| 53 | 恩普乔布                                 | startDate                    | 读取           |
| 54 | 恩普乔布                                 | jobTitle                     | 读取           |
| 55 | 恩普乔布                                 | position                     | 读取           |
| 65 | 恩普乔布                                 | 自定义String13               | 读取           |
| 56 | 恩普乔布                                 | 经理 Id                    | 读取           |
| 57 | 恩普伯\.业务单位                   | 业务单位                 | 读取           |
| 58 | 恩普伯\.业务单位                   | 企业单位Id               | 读取           |
| 59 | 恩普约\.布公司                        | company                      | 读取           |
| 60 | 恩普约\.布公司                        | companyId                    | 读取           |
| 61 | EmpJob\.\.公司注册国家 | 两个查尔国家代码           | 读取           |
| 62 | 恩普职位\.成本中心                     | costCenter                   | 读取           |
| 63 | 恩普职位\.成本中心                     | 成本中心Id                 | 读取           |
| 64 | 恩普职位\.成本中心                     | 成本中心描述        | 读取           |
| 65 | 恩普约\.布部                     | department                   | 读取           |
| 66 | 恩普约\.布部                     | departmentId                 | 读取           |
| 67 | 恩普约\.布部门                       | division                     | 读取           |
| 68 | 恩普约\.布部门                       | 部门 Id                   | 读取           |
| 69 | 恩普作业\.代码                        | 作业代码                      | 读取           |
| 70 | 恩普作业\.代码                        | 工作代码Id                    | 读取           |
| 71 | 恩普伯\.位置                       | 位置名称                 | 读取           |
| 72 | 恩普伯\.位置                       | 办公地点地址        | 读取           |
| 73 | 恩普伯\.位置                       | 办公地点城市           | 读取           |
| 74 | 恩普伯\.位置                       | 办公地点自定义字符串4  | 读取           |
| 75 | 恩普伯\.位置                       | 办公地点邮政编码        | 读取           |
| 76 | 恩普工资\.等级                       | 薪酬等级                     | 读取           |
| 77 | 恩普就业终止               | 活跃就业计数       | 读取           |
| 78 | 恩普就业终止               | 最新终止日期        | 读取           |


## <a name="default-attribute-mapping"></a>默认属性映射

下表提供了上面列出的 SuccessFactors 属性和 AD/Azure AD 属性之间的默认属性映射。 在 Azure AD 预配应用"映射"边栏选项卡中，可以修改此默认映射以包括上述列表中的属性。 

| \# | 成功因素实体                  | 成功因素属性 | 默认 AD/Azure AD 属性映射   | 处理注释                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | 每人                              | 人 Id 外部         | employeeId                              | 用作匹配属性                                                                   |
| 2  | 每人                              | 佩尔森乌伊德            | \[未映射\-用作源锚点\] | 在初始同步期间，预配服务将人库id 链接到现有对象 Guid_。  |
| 3  | 人均                            | displayName              | displayName                             | NA                                                                                           |
| 4  | 人均                            | firstName                | givenName                               | NA                                                                                           |
| 5  | 人均                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | 用户                                   | 地址Line1             | streetAddress                           | NA                                                                                           |
| 7  | 用户                                   | city                     | l                                       | NA                                                                                           |
| 8  | 用户                                   | country                  | co                                      | NA                                                                                           |
| 9  | 用户                                   | state                    | st                                      | NA                                                                                           |
| 10 | 用户                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | 用户                                   | 邮政编码                  | postalCode                              | NA                                                                                           |
| 12 | 每封电子邮件                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | 恩普乔布                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | 恩普乔布                                 | 经理 Id                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.\.公司注册国家 | 两个查尔国家代码       | c                                       | NA                                                                                           |
| 16 | 恩普约\.布部                     | department               | department                              | NA                                                                                           |
| 17 | 恩普约\.布部门                       | division                 | company                                 | NA                                                                                           |
| 18 | 恩普伯\.位置                       | 办公地点地址    | streetAddress                           | NA                                                                                           |
| 19 | 恩普伯\.位置                       | 办公地点邮政编码    | postalCode                              | NA                                                                                           |
| 20 | 恩普就业终止               | 活跃就业计数   | accountEnabled                          | 如果活动就业统计\0，请禁用该帐户*。                                           |

