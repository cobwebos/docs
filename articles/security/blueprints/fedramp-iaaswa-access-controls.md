---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 访问控制
description: FedRAMP Web 应用程序自动化 - 访问控制
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 3d30f889c4d7ed0dafcf8559e8987090c03f4d5e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="access-control-ac"></a>访问控制 (AC)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 控制 AC-1

#### <a name="access-control-policy-and-procedures"></a>访问控制策略和过程

AC-1 组织制定和记录访问控制策略，并将其分发给[分配：组织定义的人员或角色]，以解决用途、范围、角色、职责、管理承诺、组织实体之间的协调和符合性；促进访问控制策略及其关联的访问控制的实施过程；审阅和更新当前访问控制策略[分配：组织定义的频率]；访问控制过程[分配：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略和过程可能足以应对此控制措施。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 控制 AC-2.a

#### <a name="account-management"></a>帐户管理

AC-2.a 组织确定并选择以下类型的信息系统帐户，以支持组织的任务/业务职能：[分配：组织定义的信息系统帐户类型]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图依赖并实现以下系统帐户类型：Azure Active Directory 用户（用于部署解决方案并管理对 Azure 资源的访问）、Windows 操作系统用户（由 Active Directory 管理）、SQL Server 服务帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 控制 AC-2.b

#### <a name="account-management"></a>帐户管理

AC-2.b 组织为信息系统帐户分配帐户管理员。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责将管理员分配给在 AC-02.a 中标识的帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 控制 AC-2.c

#### <a name="account-management"></a>帐户管理

AC-2.c 组织确定针对组和角色成员身份的条件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责为客户控制的帐户类型确定角色和组成员身份条件（请参见 AC-02.a）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 控制 AC-2.d

#### <a name="account-management"></a>帐户管理

AC-2.d 组织指定信息系统、组和角色成员身份的授权用户，以及每个帐户的访问授权（即特权）和其他属性（根据需要）。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于既定的企业级帐户授权流程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 控制 AC-2.e

#### <a name="account-management"></a>帐户管理

AC-2.e 组织需要[分配：组织定义的人员或角色]对创建信息系统帐户的请求进行审批。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于既定的企业级帐户授权流程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 控制 AC-2.f

#### <a name="account-management"></a>帐户管理

AC-2.f 组织根据[分配：组织定义的过程或条件]创建、启用、修改、禁用和删除信息系统帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于既定的企业级帐户管理过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 控制 AC-2.g

#### <a name="account-management"></a>帐户管理

AC-2.g 组织监视信息系统帐户的使用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了安全和审核解决方案的“标识和访问”仪表板。 通过此仪表板可使帐户管理员监视信息系统帐户的使用情况。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 控制 AC-2.h

#### <a name="account-management"></a>帐户管理

AC-2.h 在这些情况下，组织通知帐户管理员：不再需要帐户时；终止或转移用户时；以及个人信息系统使用情况或“须知”范围有更改时。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以创建一个过程，以在不再需要帐户时通知适当的帐户管理员。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 控制 AC-2.i

#### <a name="account-management"></a>帐户管理

AC-2.i 组织根据有效的访问授权来授权对信息系统的访问；预期的系统使用情况；以及组织或相关任务/业务职能所要求的其他属性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以创建访问授权过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 控制 AC-2.j

#### <a name="account-management"></a>帐户管理

AC-2.j 组织检查帐户是否符合帐户管理要求[分配：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责按要求的频率检查客户控制的帐户，以确定帐户是否符合所有的组织要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 控制 AC-2.k

#### <a name="account-management"></a>帐户管理

AC-2.k 从组中删除个人时，组织建立重新签发共享/组帐户凭据的过程（如果已部署）。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以创建管理组帐户凭据的过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 控制 AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>帐户管理 | 自动系统帐户管理

AC-2 (1) 组织使用自动化的机制来支持信息系统帐户的管理。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了安全和审核解决方案的“标识和访问”仪表板。 通过此仪表板可使帐户管理员监视信息系统帐户的使用情况。 它可配置为在怀疑有异常活动或发生其他预定义事件时发送警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 控制 AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>帐户管理 | 删除临时帐户/紧急帐户

AC-2 (2) 信息系统在[分配：组织定义的每种帐户类型的时段]之后自动[选择：删除；禁用]临时和紧急帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图不部署临时或紧急帐户。 如果未手动禁用，则部署的域控制器会在 35 天后自动禁用所有的非活动帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 控制 AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>帐户管理 | 禁用非活动帐户

AC-2 (3) 信息系统在[分配：组织定义的时段]之后自动禁用非活动帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的域控制器配置为禁用在 35 天不活动的所有用户帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 控制 AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>帐户管理 |自动审核操作

AC-2 (4) 信息系统自动审核帐户创建、修改、启用、禁用和删除操作，并通知[分配：组织定义的人员或角色]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现以下系统帐户类型：Azure Active Directory 用户、Windows OS 用户、SQL Server 服务帐户。 Azure Active Directory 帐户管理操作在 Azure 活动日志中生成事件；OS 级帐户管理操作在系统日志中生成事件。 这些日志由 Log Analytics 收集，并存储在 Log Analytics 工作区中。 Log Analytics 可以配置为在预定义的事件发生时发送警报。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 控制 AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>帐户管理 | 非活动状态注销

AC-2 (5) 组织要求用户在[分配：组织定义的预期不活动时段或描述的注销时间]时注销。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以创建一项策略，使用户预计在一段时间内（或由于其他因素）不活动时注销。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 控制 AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>帐户管理 | 基于角色的方案

AC-2 (7).a 组织根据基于角色的访问方案来创建和管理特权用户帐户，并可将允许的信息系统访问和特权组织到角色中。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现以下系统帐户类型：Azure Active Directory 用户、Windows OS 用户、SQL Server 服务帐户。 Azure Active Directory 帐户特权是通过将用户分配给角色使用基于角色的访问控制来实现的；Active Directory 帐户特权是通过将用户分配给安全组使用基于角色的访问控制来实现的。 这些基于角色的方案可以由客户进行扩展以满足任务需求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 控制 AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>帐户管理 | 基于角色的方案

AC-2 (7).b 组织监视特权角色分配。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了安全和审核解决方案的“标识和访问”仪表板。 通过此仪表板可使帐户管理员监视信息系统帐户的使用情况。 可以查询此解决方案以报告特权角色分配。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 控制 AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>帐户管理 | 基于角色的方案

AC-2 (7).c 特权角色分配不再适合时，组织将采取[分配：组织定义的操作]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 特权角色分配不再适合时，客户负责对客户控制的帐户采取操作。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 控制 AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>帐户管理 | 使用共享/组帐户的限制

AC-2 (9) 组织只允许使用符合[分配：组织定义的用来创建共享/组帐户的条件]的共享/组帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的资源上未启用共享/组帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 控制 AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>帐户管理 | 共享/组帐户凭据终止

AC-2 (10) 信息系统在成员离开组时终止共享/组帐户凭据。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的资源上未启用共享/组帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 控制 AC-2 (11)

#### <a name="account-management--usage-conditions"></a>帐户管理 | 使用条件

AC-2 (11) 信息系统为[分配：组织定义的信息系统帐户]执行[分配：组织定义的环境和/或使用条件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 可以在 Active Directory 中创建组策略，并将其配置为实施时间限制或其他帐户使用条件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 控制 AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>帐户管理 | 帐户监视/异常使用

AC-2 (12).a 组织监视信息系统帐户的[分配：组织定义的异常使用]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了安全和审核解决方案的“标识和访问”仪表板。 通过此仪表板可使帐户管理员监视对已部署资源的访问尝试。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 控制 AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>帐户管理 | 帐户监视/异常使用

AC-2 (12).b 组织向[分配：组织定义的人员或角色]报告信息系统帐户的异常使用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了安全和审核解决方案的“标识和访问”仪表板。 通过此仪表板可使帐户管理员监视对已部署资源的访问尝试。 此解决方案可配置为在怀疑有异常活动或发生其他预定义事件时发送警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 控制 AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>帐户管理 | 禁用高风险个人的帐户

AC-2 (13) 组织在发现风险的[分配：组织定义的时段]内禁用造成重大风险的用户帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略和过程可以针对给组织造成重大风险的用户帐户创建禁用帐户的条件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 控制 AC-3

#### <a name="access-enforcement"></a>执法机构

AC-3 根据适用的访问控制策略，信息系统强制执行已批准的授权，以便对信息和系统资源进行逻辑访问。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图使用 Azure Active Directory 实施的基于角色的访问控制，通过将用户分配给角色来强制执行逻辑访问授权，通过将用户分配给安全组和 Windows OS 级控制来强制执行 Active Directory。 分配给用户或组的 Azure Active Directory 角色在资源、组或订阅级别控制对 Azure 内资源的逻辑访问。 Active Directory 安全组控制对 OS 级别的资源和功能的逻辑访问。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 控制 AC-4

#### <a name="information-flow-enforcement"></a>信息流强制

AC-4 根据[分配：组织定义的信息流控制策略]，信息系统强制执行已批准的授权，以控制系统内和互联系统之间的信息流。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 通过使用应用于部署资源的子网的网络安全组、应用程序网关和负载平衡器，此蓝图强制执行信息流限制。 网络安全组基于已批准的规则确保信息流在资源之间是受控的。 应用程序网关和负载平衡器根据已批准的角色动态地将流量路由到特定的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 控制 AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>信息流强制 | 安全策略筛选器

AC-4 (8) 信息系统将[分配：组织定义的安全策略筛选器]用作[分配：组织定义的信息流]的流控制决策的基础，以便强制执行信息流控制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源内强制执行信息流控制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 控制 AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>信息流强制 | 信息流的物理/逻辑分离

AC-4 (21) 信息系统使用[分配：组织定义的机制和/或技术]以逻辑或物理方式分离信息流，以完成[分配：组织定义的按信息类型的所需分离]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源内分离信息流。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 控制 AC-5.a

#### <a name="separation-of-duties"></a>职责分离

AC-5.a 组织分离[分配：组织定义的个人职责]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分离客户控制的帐户上的职责。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 控制 AC-5.b

#### <a name="separation-of-duties"></a>职责分离

AC-5.b 组织记录个人职责的分离。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责记录分离客户控制的帐户上的职责。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 控制 AC-5.c

#### <a name="separation-of-duties"></a>职责分离

AC-5.c 组织定义信息系统访问授权以支持职责分离。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现了基于角色的访问控制，可以将其配置为根据组织需求分离职责。 Azure Active Directory 帐户特权是通过将用户分配给角色使用基于角色的访问控制来实现的；Active Directory 帐户特权是通过将用户分配给安全组使用基于角色的访问控制来实现的。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 控制 AC-6

#### <a name="least-privilege"></a>最小特权

AC-6 组织采用最小特权原则，只允许对根据组织任务和业务职能完成分配任务所需的用户（或代表用户进行的过程）授予访问权限。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现基于角色的访问控制，以将用户限制为仅显式分配的特权。 Azure Active Directory 帐户特权是通过将用户分配给角色使用基于角色的访问控制来实现的；Active Directory 帐户特权是通过将用户分配给安全组使用基于角色的访问控制来实现的。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 控制 AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>最小特权 | 授权对安全功能的访问

AC-6 (1) 组织显式授予访问[分配：组织定义的安全功能（部署在硬件、软件和固件中）和安全相关信息]的权限。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以创建包含访问安全功能的访问授权过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 控制 AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>最小特权 | 非安全功能的非特权访问

AC-6 (2) 访问非安全功能时，组织要求具有[分配：组织定义的安全功能或安全相关信息]访问权限的信息系统帐户或角色，使用非特权帐户或角色。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可能要求用户在访问非安全功能时使用非特权帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 控制 AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>最小特权 | 特权命令的网络访问

AC-6 (3) 组织仅对[分配：组织定义的令人信服的运营需求]授予[分配：组织定义的特权命令]的网络访问权限，并将此类访问权限的理由记录在信息系统的安全计划中。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以定义通过网络访问的特权命令。 注意：客户没有对 Azure 基础结构的物理访问权限。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 控制 AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>最小特权 | 特权帐户

AC-6 (5) 组织限制信息系统的特权帐户为[分配：组织定义的人员或角色]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以定义使用特权帐户的限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 控制 AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>最小特权 | 用户特权评审

AC-6 (7).a 组织评审授予[分配：组织定义的角色或用户类]特权的[分配：组织定义的频率]，以验证是否需要这些权限。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审客户控制帐户的用户特权。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 控制 AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>最小特权 | 用户特权评审

AC-6 (7).b 如有必要，组织可以重新分配或删除特权，以正确反映组织的任务/业务需求。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在适当时重新分配或删除客户控制的帐户的特权。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 控制 AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>最小特权 | 代码执行的特权等级

AC-6 (8) 信息系统防止[分配：组织定义的软件]以高于用户执行软件的特权等级执行。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现基于角色的访问控制，以将用户限制为仅显式分配的特权。 虚拟机 OS 级保护不允许软件以高于用户执行该软件的特权等级执行。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 控制 AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>最小特权 | 审核使用特权功能

AC-6 (9) 信息系统审核特权功能的执行。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图可实现 Log Analytics 服务。 已部署的 VM 和 Azure 诊断存储帐户是连接到 Log Analytics 的源，确保审核特权功能的执行。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 控制 AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>最小特权 | 禁止非特权用户执行特权功能

AC-6 (10) 信息系统防止非特权用户执行特权功能，包括禁用、避开或改变已实施的安全措施/对策。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现基于角色的访问控制，以将用户限制为仅显式分配的特权。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 控制 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>失败的登录尝试次数

AC-7.a 信息系统强制执行用户在[分配：组织定义的时段]内进行的连续无效登录尝试[分配：组织定义的数量]限制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure 门户将限制用户的连续无效登录尝试次数。 在此蓝图部署的所有虚拟机的操作系统级别应用组策略。 该策略限制用户在 15 分钟内的连续无效登录尝试次数不超过 3 次。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 控制 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>失败的登录尝试次数

AC-7.b 超过最大失败尝试次数时，信息系统自动[选择：在[分配：组织定义的时段]内锁定帐户/节点；锁定帐户/节点直到管理员释放；根据[分配：组织定义的延迟算法]延迟下个登录提示]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure 门户将在用户连续无效登录尝试后锁定帐户。 在此蓝图部署的所有虚拟机的操作系统级别应用组策略。 该策略将在用户连续三次无效登录尝试后锁定帐户三个小时。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 控制 AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>失败登录尝试 | 清除/擦除移动设备

AC-7 (2) 在[分配：组织定义的次数]次连续的设备登录尝试失败之后，信息系统将根据[分配：组织定义的清除/擦除要求/技术]从[分配：组织定义的移动设备]中清除/擦除信息。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 移动设备不在 Azure 上部署的系统范围内。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Azure 边界内使用移动设备。 因此，此控制措施不适用于 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 控制 AC-8.a

#### <a name="system-use-notification"></a>系统使用通知

AC-8.a 在授予信息系统访问权限之前，该系统向用户显示[分配：组织定义的系统使用通知消息或横幅]，该系统提供与适用的联邦法律、行政命令、指令、政策、法规、标准和指导相一致的隐私和安全通知，并指出用户正在访问美国政府信息系统；信息系统的使用可能会受到监视、记录和审核；禁止未经授权使用信息系统，否则会受到刑事和民事处罚；使用信息系统即表明同意监视和记录。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 组策略实现了在登录之前向用户显示的系统使用通知。 注意：Blueprint 实现了示例系统使用通知。 客户必须编辑此本文以符合组织和/或监管机构的要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 控制 AC-8.b

#### <a name="system-use-notification"></a>系统使用通知

AC-8.b 信息系统将在屏幕上保留通知消息或横幅，直到用户同意使用条件并采取明确的行动以登录或进一步访问信息系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 组策略实现了在登录之前向用户显示的系统使用通知。 用户必须确认通知才能登录。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 控制 AC-8.c

#### <a name="system-use-notification"></a>系统使用通知

AC-8.c 在授予更多访问权限之前，可公开访问系统的信息系统将显示系统使用信息[分配：组织定义的条件]；显示与通常禁止那些活动的系统的隐私调整一致的监视、记录或审核的引用（如果有）；以及包括对系统授权使用的说明。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的所有可公开访问的资源上显示系统使用通知。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 控制 AC-10

#### <a name="concurrent-session-control"></a>并发会话控制

AC-10 信息系统将每个[分配：组织定义的帐户和/或帐户类型]的并发会话数限制为[分配：组织定义的数量]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 针对此蓝图部署的虚拟机实施操作系统策略。 此策略将实现并发会话限制（两个会话）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 控制 AC-11.a

#### <a name="session-lock"></a>会话锁定

AC-11.a 通过在非活动[分配：组织定义的时段]之后或收到来自用户的请求时启动会话锁定，信息系统阻止进一步访问系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 组策略实施 RDP 会话的非活动锁定。 用户可以手动启动该锁定。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 控制 AC-11.b

#### <a name="session-lock"></a>会话锁定

AC-11.b 信息系统将保留会话锁定，直到用户使用已建立的标识和身份验证过程重新创建访问。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 组策略实施 RDP 会话的非活动锁定。 用户必须重新进行身份验证才可解锁会话。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 控制 AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>会话锁 | 模式隐藏显示

AC-11 (1) 信息系统通过会话锁定将以前在显示器上可见的信息与公众可查看的图像一起隐藏。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 组策略实施 RDP 会话的非活动锁定。 该会话将锁定隐藏之前可见的信息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 控制 AC-12

#### <a name="session-termination"></a>会话终止

AC-12 信息系统在[分配：组织定义的条件或需要会话断开的触发事件]之后自动终止用户会话。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 可以配置由此蓝图部署的 Windows 虚拟机的远程桌面会话主机配置，以满足组织会话终止要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 控制 AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>会话终止 | 用户启动的注销/消息显示

AC-12 (1).a 无论何时使用身份验证访问[分配：组织定义的信息资源]，信息系统都提供用户启动的通信会话的注销功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的 Azure 门户和虚拟机操作系统启用启动注销的使用情况。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 控制 AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>会话终止 | 用户启动的注销/消息显示

AC-12 (1).b 信息系统向用户显示一条显式注销消息，指明可靠地终止经身份验证的通信会话。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的 Azure 门户和虚拟机操作系统启用启动注销的使用情况。 注销过程向用户提供会话已经终止的指示。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 控制 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>允许的操作，无需标识或身份验证

AC-14.a 组织识别可以在信息系统上执行的[分配：组织定义的用户操作]，无需与组织任务/业务职能一致的标识或身份验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责识别可以在客户部署的资源上执行的操作，无需识别或身份验证（例如，查看可公开访问的网页）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 控制 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>允许的操作，无需标识或身份验证

AC-14.b 组织在信息系统的安全计划中记录和提供支持的理由，不需要用户执行标识或身份验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责提供用户操作的文档，而不需要对客户部署的资源执行识别或身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 控制 AC-17.a

#### <a name="remote-access"></a>远程访问

AC-17.a 组织建立并记录每种类型的远程访问允许的使用限制、配置/连接要求和实施指导。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以定义远程访问使用限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 控制 AC-17.b

#### <a name="remote-access"></a>远程访问

AC-17.b 该组织先授权到信息系统的远程访问，然后允许此类连接。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以创建远程访问授权过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 控制 AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>远程访问 | 自动监视/控制

AC-17 (1) 信息系统监视和控制远程访问方法。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图通过 Azure 门户、通过使用 jumpbox 实现的远程桌面连接、通过客户实现的 Web 应用提供对信息系统的远程访问。 审核通过 Azure 门户和远程桌面会话进行的访问并可以通过 Log Analytics 对其进行监视。 客户必须实现到 Web 应用的远程访问控制（如有必要）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 控制 AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>远程访问 | 使用加密保护保密性/完整性

AC-17 (2) 信息系统实施加密机制，以保护远程访问会话的保密性和完整性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 对此蓝图部署的资源（包括 Azure 门户、远程桌面连接和 Web 应用程序网关）的远程访问使用 TLS 进行保护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 控制 AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>远程访问 | 托管的访问控制点

AC-17 (3) 信息系统通过[分配：组织定义的数量]个托管的网络访问控制点路由所有远程访问。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 到此蓝图部署的名义 Web 应用的远程访问是通过应用程序网关实现的。 到所有其他资源的远程访问是通过 jumpbox 实现的。 没有任何其他可公开访问的终结点。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 控制 AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>远程访问 | 特权命令/访问

AC-17 (4).a 组织通过仅对[分配：组织定义的需求]的远程访问授权特权命令的执行和安全相关信息的访问。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以定义能远程访问的特权命令并包括理由。 注意：客户没有到 Azure 基础结构的直接网络访问权限。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 控制 AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>远程访问 | 特权命令/访问

AC-17 (4).b 组织在信息系统的安全计划中记录此类访问的理由。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可以定义能远程访问的特权命令并包括理由。 注意：客户没有到 Azure 基础结构的直接网络访问权限。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 控制 AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>远程访问 | 断开/禁用访问权限

AC-17 (9) 组织提供了在[分配：组织定义的时段]内迅速断开或禁止到信息系统的远程访问的功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图通过 Azure 门户、通过使用 jumpbox 实现的远程桌面连接、通过 Web 应用提供对信息系统的远程访问。 如果禁用或删除 Azure Active Directory 帐户，则 Azure 门户访问会立即断开。 同样，如果禁用或删除虚拟机 OS 级帐户，通过 jumpbox 的远程桌面访问将立即断开。 客户必须为 Web 应用实施远程访问断开。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 控制 AC-18.a

#### <a name="wireless-access"></a>无线访问

AC-18.a 组织建立无线访问的使用限制、配置/连接要求和实施指导。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 通过网络安全标准创建无线访问的使用限制、配置/连接要求和实施指导，显式禁止在 Microsoft Azure 环境中使用无线。 |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 控制 AC-18.b

#### <a name="wireless-access"></a>无线访问

AC-18.b 该组织先授权到信息系统的无线访问，然后允许此类连接。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Microsoft Azure 数据中心内进行无线访问。 |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 控制 AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>无线访问 | 身份验证和加密

AC-18 (1) 信息系统使用[选择（一个或多个）：用户；设备]的身份验证和加密来保护对系统的无线访问。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Microsoft Azure 环境中进行无线访问。 |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 控制 AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>无线访问 | 禁用无线网络

AC-18 (3) 在不打算使用的情况下，组织在发布和部署之前将禁用从内部嵌入到信息系统组件的无线网络功能。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Microsoft Azure 环境中进行无线访问。 |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 控制 AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>无线访问 | 限制用户配置

AC-18 (4) 组织识别并显式授权允许独立配置无线网络功能的用户。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Microsoft Azure 环境中进行无线访问。 |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 控制 AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>无线访问 | 天线/传输功率电平

AC-18 (5) 组织选择无线电天线并校准传输功率电平，以降低在组织控制的边界之外收到可用信号的可能性。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有无线访问。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Microsoft Azure 环境中进行无线访问。 |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 控制 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>移动设备的访问控制

AC-19.a 组织建立组织控制的设备的使用限制、配置要求、连接要求和实施指导。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有客户控制的移动设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Azure 边界内使用移动设备。 因此，此控制措施不适用于 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 控制 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>移动设备的访问控制

AC-19.b 组织授权移动设备到组织信息系统的连接。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有客户控制的移动设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Azure 边界内使用移动设备。 因此，此控制不适用于 Microsoft Azure。 |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 控制 AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>移动设备的访问控制 | 全设备/基于容器的加密

AC-19 (5) 组织采用[选择：全设备加密；容器加密]来保护[分配：组织定义的移动设备]上信息的保密性和完整性。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有客户控制的移动设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 不允许在 Azure 边界内使用移动设备。 因此，此控制措施不适用于 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 控制 AC-20.a

#### <a name="use-of-external-information-systems"></a>使用外部信息系统

AC-20.a 组织建立符合拥有、运营和/或维护外部信息系统的其他组织建立的信任关系的条款和条件，允许授权的个人从外部信息系统访问信息系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可包含有关在 FedRAMP 下使用云服务产品的设置。 FedRAMP 联合授权委员会 (JAB) 已授予 Azure 暂时运营授权 (P-ATO)，使政府机构能够获得 Azure 云服务的使用权。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 控制 AC-20.b

#### <a name="use-of-external-information-systems"></a>使用外部信息系统

AC-20.b 组织建立符合拥有、运营和/或维护外部信息系统的其他组织建立的信任关系的条款和条件，允许授权的个人使用外部信息系统处理、存储或传输组织控制的信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可包含有关在 FedRAMP 下使用云服务产品的设置。 FedRAMP 联合授权委员会 (JAB) 已授予 Azure 暂时运营授权 (P-ATO)，使政府机构能够获得 Azure 云服务的使用权。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 控制 AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>使用外部信息系统 | 授权使用的限制

AC-20 (1) 仅当组织如其信息安全策略和安全计划中规定验证对外部系统实施所需的安全控制时，组织才允许授权的个人使用外部信息系统访问信息系统或处理、存储、传输组织的信息；或者保留已批准的信息系统连接，或处理与托管外部信息系统的组织实体之间的协议。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级信息技术组可以验证云服务提供程序是否符合组织信息安全要求，并授予企业范围的批准以使用相关的云服务产品。 FedRAMP 联合授权委员会 (JAB) 已授予 Azure 暂时运营授权 (P-ATO)。 由 FedRAMP 批准的第三方评估组织 (3PAO) 已对 Azure 进行评估，验证其符合 FedRAMP 安全控制和其他要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 控制 AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>使用外部信息系统 | 便携式存储设备

AC-20 (2) 组织[选择：限制；禁止]授权的个人在外部信息系统上使用组织控制的便携式存储设备。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft 不允许在 Microsoft Azure 环境中使用客户控制的便携式存储设备。 |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 控制 AC-21.a

#### <a name="information-sharing"></a>信息共享

AC-21.a 通过使授权用户能够确定分配给共享伙伴的访问授权是否符合[分配：需要用户酌情权的组织定义的信息共享情况]的信息访问限制，组织可以促进信息共享。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制策略可包含关于信息共享的设置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 控制 AC-21.b

#### <a name="information-sharing"></a>信息共享

AC-21.b 组织采用[分配：组织定义的自动化机制或手动流程]来帮助用户进行信息共享/协作决策。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于企业级信息共享决策支持功能。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 控制 AC-22.a

#### <a name="publicly-accessible-content"></a>可公开访问的内容

AC-22.a 组织指定已获授权将信息发布到可公开访问的信息系统上的个人。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以指定已获授权发布可公开访问的信息的个人。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 控制 AC-22.b

#### <a name="publicly-accessible-content"></a>可公开访问的内容

AC-22.b 组织培训已获授权的个人，以确保可公开访问的信息不包含非公开信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于企业级培训来指导已获授权发布可公开访问的信息的个人。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 控制 AC-22.c

#### <a name="publicly-accessible-content"></a>可公开访问的内容

AC-22.c 组织在发布到可公开访问的信息系统之前评审提议的信息内容，以确保不包括非公开信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以建立评审过程，用于审核发布到可公开访问的系统的内容。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 控制 AC-22.d

#### <a name="publicly-accessible-content"></a>可公开访问的内容

AC-22.d 组织评审可公开访问的信息系统中的内容是否有非公开信息[分配：组织定义的频率]，如果发现，将删除此类信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级访问控制过程可以建立评审过程，用于定期检查发布到可公开访问的系统的内容。 |
| **提供商 (Microsoft Azure)** | 不适用 |

