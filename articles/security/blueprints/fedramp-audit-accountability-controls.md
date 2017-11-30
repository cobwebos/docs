---
title: "FedRAMP Azure Blueprint 自动化 - 审核和责任"
description: "FedRAMP 的 Web 应用 - 审核和责任"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>审核和责任 (AU)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-au-1"></a>NIST 800-53 控制措施 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>审核和责任策略和过程

**AU-1** 组织制定、记录并向 [赋值：组织定义的个人或角色] 分发一项审核和责任策略，该策略提出目的、范围、角色、责任、管理承诺、组织实体之间的协调与符合性；设定用于实施审核和责任策略以及相关审核和责任策略控制措施的过程；按 [赋值：组织定义的频率] 的频率评审并更新当前审核和责任策略；按 [赋值：组织定义的频率] 的频率实施审核和责任过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级审核和责任策略和过程可能足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800-53 控制措施 AU-2.a

#### <a name="audit-events"></a>审核事件

**AU-2.a** 组织确定信息系统能够审核以下事件：[赋值：组织定义的可审核事件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 的审核功能由 Azure Monitor 和 OMS 中的 Log Analytics 服务提供。 Azure Monitor 提供与已部署资源相关的活动的详细审核日志。 这些日志和 OS 级别的日志由 Log Analytics 收集，并存储在 OMS 存储库中。 Log Analytics 可关联此解决方案部署的资源中的审核数据，并且可扩展到客户部署的 Web 应用程序。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800-53 控制措施 AU-2.b

#### <a name="audit-events"></a>审核事件

**AU-2.b** 组织协调其他需要审核相关信息来增强相互支持并帮助指导可审核事件选择的组织实体的安全审核职能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可能依赖已建立的企业级过程来确定可审核事件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800-53 控制措施 AU-2.c

#### <a name="audit-events"></a>审核事件

**AU-2.c** 组织提供认为可审核事件足以支持安全事件事后调查的基本原理。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 审核的事件包括足以确定事件发生时间、事件来源和事件结果的信息，以及其他支持安全事件调查的详细信息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800-53 控制措施 AU-2.d

#### <a name="audit-events"></a>审核事件

**AU-2.d** 组织确定在信息系统内审核以下事件：[赋值：组织定义的审核事件（AU-2 a 中定义的部分可审核事件）以及对每个已标识事件的审核频率（或需要审核的情形）]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 审核的事件包括通过 Azure 活动日志（针对已部署的资源）、OS 级日志、Active Directory 日志和 SQL Server 日志审核的事件。 客户可根据任务需求选择其他要审核的事件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 控制措施 AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>审核事件 | 评审和更新

**AU-2 (3)** 组织按 [赋值：组织定义的频率] 的频率评审和更新审核的事件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可依赖针对已定义的一组审核事件建立的企业级定期评审和更新过程。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800-53 控制措施 AU-3

#### <a name="content-of-audit-records"></a>审核记录的内容

**AU-3** 信息系统生成的审核记录包含以下信息：用于确定所发生事件的类型、事件发生时间、事件发生地点、事件来源和事件结果的信息，以及与事件相关的任何个人或主体的标识信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 依赖于 Azure、Windows Server 和 SQL Server 的内置审核功能。 这些审核解决方案捕获的审核记录包含充足的详细信息，可满足此控制措施的需求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 控制措施 AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>审核记录的内容 | 其他审核信息

**AU-3 (1)** 信息系统生成的审核记录包含以下附加信息：[赋值：组织定义的更多附加详细信息]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure 活动日志事件使用的详细架构中包含多个字段，对应超过 20 种审核信息。 除了活动日志之外，此 Azure Blueprint 还在 OMS 中部署了 Log Analytics 解决方案，支持 Windows 日志、Linux 日志、Azure 诊断日志和客户日志等多种数据源。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 控制措施 AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>审核记录的内容 | 集中管理计划的审核记录内容

**AU-3 (2)** 信息系统针对 [赋值：组织定义的信息系统组件] 生成的审核记录中要捕获的内容提供集中管理和配置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署的所有虚拟机都加入已部署的 Active Directory 域。 所有已加入域的虚拟机都实现一组策略，可通过配置该策略集中管理 OS 级审核系统配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800-53 控制措施 AU-4

#### <a name="audit-storage-capacity"></a>审核存储容量

**AU-4** 组织根据 [赋值：组织定义的审核记录存储需求] 分配审核记录存储容量。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 分配可保留一年审核记录的充足存储容量。 所有审核记录都由 Log Analytics 收集，并配置一年的保留期。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800-53 控制措施 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>对审核处理失败的响应

**AU-5.a** 信息系统在审核处理失败时向 [赋值：组织定义的人员或角色] 发出警报。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 可在 Azure 状态网站和 Azure 门户中的服务运行状况边栏选项卡上查看 Azure Monitor 和 Log Analytics 的服务状态。 可通过 Log Analytics 配置警报，以便提供有关其他类型的审核处理失败的通知。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800-53 控制措施 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>对审核处理失败的响应

**AU-5.b** 信息系统执行以下附加操作：[赋值：组织定义的要执行的操作（例如，关闭信息系统、覆盖最早的审核记录、停止生成审核记录）]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此 Azure Blueprint 部署的资源生成的所有审核记录都由 Log Analytics 收集，并保留一年时间。 此审核记录存储的存储分配是动态分配的，可确保有足够的容量可用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 控制措施 AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>对审核处理失败的响应 | 审核存储容量

**AU-5 (1)** 信息系统在分配的审核记录存储容量达到存储库最大审核记录存储容量的 [赋值：组织定义的百分比] 时，在 [赋值：组织定义的时间段] 内向 [赋值：组织定义的人员、角色和/或位置] 发出警告。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此 Azure Blueprint 部署的资源生成的所有审核记录都由 Log Analytics 收集，并保留一年时间。 此审核记录存储的存储分配是动态分配的，可确保有足够的容量可用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 控制措施 AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>对审核处理失败的响应 | 实时警报

**AU-5 (2)** 信息系统在发生以下审核失败事件时在 [赋值：组织定义的实时时段] 内向 [赋值：组织定义的人员、角色和/或位置] 发出警报：[赋值：组织定义的需要实时警报的审核失败事件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 可在 Azure 门户的服务运行状况边栏选项卡上查看 Azure 的服务状态。 可通过 Log Analytics 配置警报，以便提供有关其他类型的审核处理失败的通知。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800-53 控制措施 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>审核评审、分析和报告

**AU-6.a** 组织按 [赋值：组织定义的频率] 的频率评审和分析信息系统审核记录，了解指示 [赋值：组织定义的不当活动或异常活动] 的信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审和分析客户部署资源（包括应用程序、操作系统、数据库和软件）的审核记录。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 控制措施 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>审核评审、分析和报告

**AU-6.b** 组织向 [赋值：组织定义的人员或角色] 报告相关发现。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责报告在客户部署的资源上发现的不当活动或异常活动（如 AU-06.a 中的定义）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 控制措施 AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>审核评审、分析和报告 | 过程集成

**AU-6 (1)** 组织采用自动化机制集成审核评审、分析和报告过程，为调查和响应可疑活动的组织过程提供支持。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可依赖企业级的集中审核评审、分析和报告功能。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 控制措施 AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>审核评审、分析和报告 | 关联审核存储库

**AU-6 (3)** 组织分析并关联不同存储库中的审核记录，以便获得组织范围的态势感知。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 解决方案，以便集中已部署资源中的审核数据，为组织范围的态势感知提供支持。 客户可能选择进一步将 Log Analytics 与其他系统集成。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 控制措施 AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>审核评审、分析和报告 | 中心评审和分析

**AU-6 (4)** 信息系统提供集中评审和分析系统内多个组件中的审核记录的能力。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 解决方案，以集中已部署资源中的审核数据，为集中评审、分析和报告提供支持。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 控制措施 AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>审核评审、分析和报告 | 集成/扫描和监视功能

**AU-6 (5)** 组织将审核记录分析与 [选择（一个或多个）：漏洞扫描信息；性能数据；信息系统监控信息；[赋值：组织定义的从其他来源收集的数据/信息]] 的分析集成，进一步增强识别不当活动或异常活动的能力。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署 OMS 安全和审核解决方案。 此解决方案提供有关安全状况的全面信息。 安全和审核仪表板使用部署的 OMS 解决方案中的可用数据，提供对部署资源的安全状态的高级洞察，同时集成基线和修补程序评估中的日志数据和漏洞数据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 控制措施 AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>审核评审、分析和报告 | 与物理监视关联

**AU-6 (6)** 组织将审核记录中的信息与通过监视物理访问获得的信息关联，进一步增强识别可疑、不当、异常或恶意活动的能力。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 的 SIM 团队使用相关物理监视数据，并将其与审核记录关联，以确定在检测到物理事件时是否存在关联的逻辑漏洞或可疑行为。 |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 控制措施 AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>审核评审、分析和报告 | 允许的操作

**AU-6 (7)** 组织为每个与评审、分析和报告审核信息关联的 [选择（一个或多个）：信息系统过程；角色；用户] 指定允许的操作。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此 Azure Blueprint 部署的 Windows 虚拟机实现 OS 级权限，限制用户可对审核信息采取的操作。 在 Azure 中，可将用户或用户组分配到各角色（例如，所有者、参与者、读者或自定义角色），以限制可对任何资源或部署的解决方案（包括 Log Analytics ）采取的操作。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 控制措施 AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>审核评审、分析和报告 | 审核级别调整

**AU-6 (10)** 根据执法信息、情报信息或其他可靠信息来源得知风险发生变化时，组织在信息系统内调整审核评审、分析和报告级别。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 根据执法信息、情报信息或其他可靠信息来源得知风险发生变化时，客户负责调整客户部署资源（包括应用程序、操作系统、数据库和软件）的审核评审、分析和报告级别。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800-53 控制措施 AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>审核缩减和报表生成

**AU-7.a** 信息系统提供审核缩减和报表生成功能，支持按需审核评审、分析和报告需求，以及安全事件的事后调查。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 解决方案。 Log Analytics 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 收集后，可以分析、导出数据或针对它们发出警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800-53 控制措施 AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>审核缩减和报表生成

**AU-7.b** 信息系统提供审核缩减和报表生成功能，不会改变审核记录的原始内容或时间排序。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 解决方案。 Log Analytics 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 通过 Log Analytics 进行收集时，不会更改审核记录的内容和时间排序。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 控制措施 AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>审核缩减和报表生成 | 自动处理

**AU-7 (1)** 信息系统提供基于 [赋值：审核记录内组织定义的审核字段] 处理相关事件的审核记录的能力。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 解决方案。 Log Analytics 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 收集后，可以分析、导出数据或针对它们发出警报。 Log Analytics 包含强大的查询语言，可提取存储库中存储的数据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800-53 控制措施 AU-8.a

#### <a name="time-stamps"></a>时间戳

**AU-8.a** 信息系统使用内部系统时钟生成审核记录的时间戳。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署的资源使用内部系统时钟生成审核记录的时间戳。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800-53 控制措施 AU-8.b

#### <a name="time-stamps"></a>时间戳

**AU-8.b** 信息系统记录审核记录的时间戳，该时间戳可映射到协调世界时 (UTC) 或格林威治标准时间 (GMT)，并满足 [赋值：组织定义的时间度量粒度]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署的资源使用内部系统时钟生成审核记录的时间戳。 时间戳以 UTC 格式记录。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800-53 控制措施 AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>时间戳 | 与权威时间源同步

**AU-8 (1).a** 信息系统以 [赋值：组织定义的频率] 的频率将内部信息系统时钟与 [赋值：组织定义的权威时间源] 进行比较。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署的资源使用内部系统时钟生成审核记录的时间戳。 内部系统时钟配置为与权威时间源同步。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800-53 控制措施 AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>时间戳 | 与权威时间源同步

**AU-8 (1).b** 信息系统在时间差异大于 [赋值：组织定义的时间段] 时，将内部系统时钟与权威时间源同步。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 部署的资源使用内部系统时钟生成审核记录的时间戳。 内部系统时钟配置为与权威时间源同步。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800-53 控制措施 AU-9

#### <a name="protection-of-audit-information"></a>审核信息保护

**AU-9** 信息系统保护审核信息和审核工具免遭未经授权的访问、修改和删除。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 逻辑访问控制用于防止此 Azure Blueprint 内的审核信息和工具遭到未经授权的访问、修改和删除。 Azure Active Directory 使用基于角色的组成员身份强制执行已批准的逻辑访问。 查看审核信息和使用审核工具的能力仅限于需要这些权限的用户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 控制措施 AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>审核信息保护 | 对独立物理系统/组件的审核备份

**AU-9 (2)** 信息系统以 [赋值：组织定义的频率] 的频率将审核记录备份到与被审核的系统或组件物理不同的系统或系统组件上。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 服务。 部署的 VM 和 Azure 诊断存储帐户连接到 Log Analytics 的源，并与其来源分开保留。 数据由 OMS 近乎实时地进行收集。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 控制措施 AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>审核信息保护 | 加密保护

**AU-9 (3)** 信息系统实现加密机制，以保护审核信息和审核工具的完整性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 服务。 Log Analytics 通过使用 Azure 身份验证对证书和数据完整性进行验证，确保传入数据来自受信任的源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 控制措施 AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>审核信息保护 | 特权用户子集的访问权限

**AU-9 (4)** 组织仅向 [赋值：组织定义的特权用户子集] 授予对审核管理功能的访问权限。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 逻辑访问控制用于防止此 Azure Blueprint 内的审核信息和工具遭到未经授权的访问、修改和删除。 Azure Active Directory 使用基于角色的组成员身份强制执行已批准的逻辑访问。 查看审核信息和使用审核工具的能力仅限于需要这些权限的用户。
 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800-53 控制措施 AU-10

#### <a name="non-repudiation"></a>不可否认性

**AU-10** 信息系统可防止个人（或代表个人执行的过程）错误地否认执行了 [赋值:组织定义的不可否认性范围内的行为]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 的审核功能由 Azure Monitor 和 OMS 中的 Log Analytics 服务提供。 Azure Monitor 提供与已部署资源相关的活动的详细审核日志。 这些日志和 OS 级别的日志由 Log Analytics 收集，并存储在 OMS 存储库中。 这些日志包含信息系统事件的详细记录，有助于防范不可否认性。 此外，使用基于角色的访问控制来限制对日志数据的访问，防止对日志数据进行未经授权的修改或删除。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800-53 控制措施 AU-11

#### <a name="audit-record-retention"></a>审核记录保留期

**AU-11** 组织将审核记录保留 [赋值：组织定义的符合记录保留策略的时间段]，为安全事件的事后调查提供支持，并满足法规要求和组织的信息保留要求。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 服务。 Log Analytics 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 收集数据后，每个 Log Analytics 配置保留一年。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800-53 控制措施 AU-12.a

#### <a name="audit-generation"></a>审核生成

**AU-12.a** 信息系统在 [赋值：组织定义的信息系统组件] 中为 AU-2 a 中定义的可审核事件提供审核记录生成功能。 at [Assignment: organization-defined information system components].

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 审核的事件包括通过 Azure 活动日志（针对已部署的资源）、OS 级日志、Active Directory 日志和 SQL Server 日志审核的事件。 客户可根据任务需求选择其他要审核的事件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800-53 控制措施 AU-12.b

#### <a name="audit-generation"></a>审核生成

**AU-12.b** 信息系统允许 [赋值：组织定义的人员或角色] 选择信息系统的特定组件要审核的可审核事件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 使用基于角色的访问控制在 Azure 和虚拟机 OS 级限制对审核功能的访问。 拥有基于角色的相应授权的用户可以配置由此 Azure Blueprint 部署的资源审核的所选事件配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800-53 控制措施 AU-12.c

#### <a name="audit-generation"></a>审核生成

**AU-12.c** 信息系统使用 AU-3 中定义的内容为 AU-2.d 中定义的事件生成审核记录。 with the content defined in AU-3.

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 审核的事件包括通过 Azure 活动日志（针对已部署的资源）、OS 级日志、Active Directory 日志和 SQL Server 日志审核的事件。 客户可根据任务需求选择其他要审核的事件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 控制措施 AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>审核生成 | 系统范围/时间相关的审核跟踪

**AU-12 (1)** 信息系统将 [赋值：组织定义的信息系统组件] 中的审核记录编译为系统范围的（逻辑或物理）审核线索，该审核线索在 [赋值：审核线索中各记录时间戳之间的关系的系统定义的容错级别] 内与时间相关。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此 Azure Blueprint 在 OMS 中实现 Log Analytics 服务。 Log Analytics 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 审核记录时间戳不会被更改，因此审核线索是时间相关的。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 控制措施 AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>审核生成 | 已获授权的个人的更改

**AU-12 (3)** 信息系统为 [赋值：组织定义的个人或角色] 提供在 [赋值：组织定义的时间阈值] 内基于 [赋值：组织定义的可选事件标准] 更改要在 [赋值：组织定义的信息系统组件] 中执行的审核的功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 使用基于角色的访问控制在 Azure 和虚拟机 OS 级限制对审核功能的访问。 拥有基于角色的相应授权的用户可以配置由此 Azure Blueprint 部署的资源审核的所选事件配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |
