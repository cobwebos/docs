---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 规划
description: FedRAMP Web 应用程序自动化 - 规划
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151092"
---
# <a name="planning-pl"></a>规划 (PL)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 控制措施 PL-1

#### <a name="security-planning-policy-and-procedures"></a>安全计划政策和程序

PL-1 组织向 [赋值：组织定义的个人或角色] 开发、记录和宣传安全计划政策，用以处理用途、范围、角色、职责、管理承诺、符合性和组织实体间协调性相关事宜，并开发、记录和宣传用以促进安全计划政策和相关安全计划控制的实施的程序；并且按 [赋值：组织定义的频率] 的频率查看和更新当前安全计划政策和安全计划程序。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级安全计划政策和程序足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 控制措施 PL-2.a

#### <a name="system-security-plan"></a>系统安全计划

PL-2.a 组织开发与组织的企业架构相一致的信息系统安全计划；明确定义该系统的授权边界；就任务和业务流程描述信息系统的操作环境；提供信息系统的安全分类，包括支持性原理；描述信息系统的操作环境，以及与其他信息系统的关系或联系；提供有关该系统的安全要求的概述；确定任何相关重叠（如适用）；描述当前的或计划的安全控制用以满足这些要求，包括调整决定的缘由；并在计划实施之前由授权官员或指定代表进行评审和批准。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责开发满足目标授权（如 FedRAMP）所定义条件的系统安全计划 (SSP)。 客户可以参考《NIST 特别出版物 800-18 R1》中的“联邦信息系统安全计划开发指南”。 客户 SSP 应处理源自 Microsoft Azure 的控制要求，并参考 Microsoft Azure SSP 了解实施详情。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 控制措施 PL-2.b

#### <a name="system-security-plan"></a>系统安全计划

PL-2.b 组织分发安全计划的副本，并向 [赋值：组织定义的个人或角色] 传达计划的后续更改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分发系统安全计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 控制措施 PL-2.c

#### <a name="system-security-plan"></a>系统安全计划

PL-2.c 组织按 [赋值：组织定义的频率] 的频率评审信息系统的安全计划。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审系统安全计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 控制措施 PL-2.d

#### <a name="system-security-plan"></a>系统安全计划

PL-2.d 组织更新计划，以处理对操作的信息系统/环境的更改，或者处理在计划实施或安全控制评估期间识别的问题。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责更新系统安全计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 控制措施 PL-2.e

#### <a name="system-security-plan"></a>系统安全计划

PL-2.e 组织保护安全计划免受未经授权的泄漏和修改的影响。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责保护系统安全计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 控制措施 PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>系统安全计划 | 与其他组织实体规划/协调

PL-2 (3) 如果组织进行的安全相关活动对信息系统产生影响，则进行此类活动前应与 [赋值：组织定义的个人或组] 一起规划和协调，降低该活动对其他组织实体的影响。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责规划和协调安全相关的活动，降低对其他组织实体的影响。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 控制措施 PL-4.a

#### <a name="rules-of-behavior"></a>行为规则

PL-4.a 组织就信息使用和信息系统使用建立描述其职责和预期行为的规则，并提供给需要访问信息系统的个人。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级行为规则足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 控制措施 PL-4.b

#### <a name="rules-of-behavior"></a>行为规则

PL-4.b 组织在授权个人访问信息和信息系统之前，接收来自此人的已签名确认信息，表示他们已阅读、理解并同意遵守行为规则。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级行为规则足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 控制措施 PL-4.c

#### <a name="rules-of-behavior"></a>行为规则

PL-4.c 组织按 [赋值：组织定义的频率] 的频率评审和更新行为规则。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级行为规则足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 控制措施 PL-4.d

#### <a name="rules-of-behavior"></a>行为规则

PL-4.d 组织要求对以前版本的行为规则进行过签名的个人在行为规则修订/更新后进行阅读和重新签名。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级行为规则足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 控制措施 PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>行为规则 | 社交媒体和网络限制

PL-4 (1) 组织在行为规则中包括有关社交媒体使用和在公共网站发布组织信息的明确限制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级行为规则可包括对社交媒体/网站的限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 控制措施 PL-8.a

#### <a name="information-security-architecture"></a>信息安全体系结构

PL-8.a 组织开发信息系统的信息安全体系结构，用于描述有关保护组织信息的机密性、完整性和可用性的总体理念、需求及要采用的方法；描述如何将信息安全体系结构集成到企业体系结构以及信息安全体系结构如何支持企业体系结构；描述任何相关信息安全假设、依赖条件和外部服务。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责为客户部署的资源开发信息安全体系结构。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 控制措施 PL-8.b

#### <a name="information-security-architecture"></a>信息安全体系结构

PL-8.b 组织按 [赋值：组织定义的频率] 的频率评审和更新信息安全体系结构，以反映企业体系结构中的更新。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审和更新信息安全体系结构。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 控制措施 PL-8.c

#### <a name="information-security-architecture"></a>信息安全体系结构

PL-8.c 组织确保规划的信息安全体系结构更改反映在安全计划、安全操作概念 (CONOPS) 和组织采购/购买中。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责规划信息安全体系结构的更改。 |
| **提供商 (Microsoft Azure)** | 不适用 |
