---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 维护
description: FedRAMP Web 应用程序自动化 - 维护
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="maintenance-ma"></a>维护 (MA)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-ma-1"></a>NIST 800-53 控制措施 MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>系统维护策略和流程

MA-1：组织制定、记录一项系统维护策略并将其分配给[赋值：组织定义的个人或角色]，目的是就用途、范围、角色、职责、管理承诺、组织实体间的协调及符合性方面进行处理；设定流程来协助系统维护策略及相关系统维护控制措施的实施；按[赋值：组织定义的频率]审核和更新当前系统维护策略；按[赋值：组织定义的频率]就系统维护流程进行处理。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护策略和流程可能足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800-53 控制措施 MA-2.a

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.a：组织依据制造商或供应商规范和/或组织要求，规划、执行、记录和审核针对信息系统组件的维护及维修记录。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施受控性维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 控制措施 MA-2.b

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.b：组织就各项维护活动进行审批和监视：是在站点上执行还是远程执行，设备是服务于站点还是迁移到其他位置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施受控性维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800-53 控制措施 MA-2.c

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.c：组织要求[赋值：组织定义的个人或角色]就是否从组织设施中移除信息系统或信息组件进行场外维护或修理进行明确审批。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 规定，需要场外操作的资产（如网络设备或服务器）需具备明确的资产所有者批准。 |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 控制措施 MA-2.d

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.d：组织就设备进行清理，删除来自相关媒体的全部信息，再移出组织设施进行场外维护或修理。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 的资产保护标准针对资产场外转移定义了必需的资产处理注意事项。 资产保护标准规定，数据存储资产在移出数据中心之前，需依照 NIST SP 800-88 媒体清理准则进行清理/清洗。 |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800-53 控制措施 MA-2.e

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.e： 组织就所有可能受影响的安全性控制措施进行检查，以验证控制措施仍旧在维护或修理操作方面正常发挥作用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施受控性维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800-53 控制措施 l MA-2.f

#### <a name="controlled-maintenance"></a>受控性维护

MA-2.f：组织在组织维护记录中填写[赋值：组织定义的与维护相关的信息]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施受控性维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-2-2a"></a>NIST 800-53 控制措施 MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>受控性维护 | 自动维护活动

MA-2 (2).a：组织采用自动化机制来规划、执行和记录维护及修理事务。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施自动化维护活动。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800-53 控制措施 MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>受控性维护 | 自动维护活动

MA-2 (2).b：组织就所请求的、已规划的、正在实施的和已完成的所有维护及修理操作制备最新、准确的完整记录。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施自动化维护活动。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800-53 控制措施 MA-3

#### <a name="maintenance-tools"></a>维护工具

MA-3：组织就信息系统维护工具进行审核、控制和监视。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 运用多种工具实施维护。 软件维护工具通过 Microsoft Azure 更改及发布流程进行审批、控制和维护。 <br /> 网站服务团队就数据中心内使用的经审批维护工具的库存进行维护（详见 MA-3）。 维护人员需使用所提供的维护工具。 需征得数据中心管理批准，方可使用非数据中心提供的工具。 物理工具（如螺丝刀和扳手等）不受此控制。 <br /> 每个设施均设有一个受限的保管箱或进出受控的房间，用于存放 FLuke EtherScope、光纤通道测试仪和以外网协调器等专用维护工具。网站服务团队实施常规库存检查，以验证所有工具的状况。 对保管箱或维护储存间的访问通过访问打卡日志进行跟踪（可在调查时使用）。 |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800-53 控制措施 MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>维护工具 | 检查工具

MA-3 (1)：组织就维护人员带入场地内进行不恰当改装或未经授权的改装的维护工具进行审查。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 的网站服务团队就数据中心内使用的经审批维护工具的库存进行维护（更多详情请参见 MA-3）。 维护人员需使用所提供的维护工具。 需征得 DCM 批准，方可使用非数据中心提供的工具。 |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800-53 控制措施 MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>维护工具 | 检查媒体

MA-3 (2)：在信息系统中使用媒体之前，组织需就具备处理恶意代码的诊断及测试程序的媒体进行检查。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 规定，在未征得数据中心管理批准的情况下，不得在 Microsoft Azure 数据中心的生产环境中使用移动计算或存储媒体。 不得在 Microsoft Azure 数据中心的生产环境中使用个人所有的媒体。 <br /> Microsoft Azure 实施了一项流程，用于在 Microsoft Azure 数据中心的生产环境中使用笔记本电脑之前对其进行检查。 接受过培训的安全管理员就在生产环境中使用笔记本电脑的人员进行检查，验证其笔记本电脑已接受且通过了检查。 |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800-53 控制措施 MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>维护工具 | 防止未经授权的移除

MA-3 (3)：组织通过以下方式来阻止未经授权地移除包含组织信息的维护设备：验证设备中未包含组织信息；清理或销毁设备；将设备保留在设施内；或征得[赋值：组织定义的个人或角色]的例外批准，其明确授权可从设施中移除设备。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 采用存放在设施内且不会被移除的数据中心专用维护工具。 每个设施均设有一个受限的保管箱或储存间，用于存放 FLuke EtherScope、光纤通道测试仪和以外网协调器等专用维护工具。DCAT 就保管箱或储存间的访问进行控制，防止未经授权使用维护工具。 <br /> 维护期间，组织信息受到 MA-4 中的控制措施保护。 用户必须具有特权帐户和验证器，才可访问组织信息。 |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 控制措施 MA-4.a

#### <a name="nonlocal-maintenance"></a>非本地维护

MA-4.a：组织就非本地维护和诊断活动进行批准和监视。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统上实施非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800-53 控制措施 MA-4.b

#### <a name="nonlocal-maintenance"></a>非本地维护

MA-4.b：仅当非本地维护和诊断工具符合组织策略并记录在信息系统的安全计划中时，组织才允许使用它们。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统上实施非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 控制措施 MA-4.c

#### <a name="nonlocal-maintenance"></a>非本地维护

MA-4.c：组织在创建非本地维护和诊断会话时使用安全级别高的验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统上实施非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800-53 控制措施 MA-4.d

#### <a name="nonlocal-maintenance"></a>非本地维护

MA-4.d：组织维护非本地维护及诊断活动的相关记录。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统上实施非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800-53 控制措施 MA-4.e

#### <a name="nonlocal-maintenance"></a>非本地维护

MA-4.e： 组织会在非本地维护完成时终止会话和网络连接。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统上实施非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 控制措施 MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>非本地维护 | 记录非本地维护

MA-4 (2)：组织在安全计划中记录建立和使用非本地维护及诊断连接时所用的信息系统、策略和流程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的操作系统的安全计划中记录非本地维护相关信息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 控制措施 MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>非本地维护 | 等效安全性/清理

MA-4 (3)：组织规定，在信息系统上实施非本地维护和诊断服务，且该信息系统实施的安全功能与所服务的系统上实施的功能相当；或者 1) 先从信息系统中移除要处理的组件，再实施非本地维护和诊断服务，2) 先清理组件（涉及到组织信息的方面），再从组织设施中移除，3) 实施服务之后，先检查并清理组件（涉及到组织信息的方面），再将组件重新连接到信息系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责通过具有等效安全性的信息系统就客户定义的操作系统实施所有非本地维护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 控制措施 MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>非本地维护 | 加密保护

MA-4 (6)：信息系统实施加密机制，以保护非本地维护及诊断通信的完整性和保密性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在就客户定义的操作系统实施非本地维护和诊断时实施加密机制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800-53 控制措施 MA-5.a

#### <a name="maintenance-personnel"></a>维护人员

MA-5.a：组织就维护人员授权设立一个流程，并就已授权维护组织或人员的名单进行维护。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护人员授权和陪同流程足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800-53 控制措施 MA-5.b

#### <a name="maintenance-personnel"></a>维护人员

MA-5.b：组织确保在信息系统上进行维护的“无陪同”人员已获得访问授权。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护人员授权和陪同流程足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800-53 控制措施 MA-5.c

#### <a name="maintenance-personnel"></a>维护人员

MA-5.c：组织指派具备所需访问授权及技术能力的组织人员，对缺少所需访问授权的人员的维护活动进行监督。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护人员授权和陪同流程足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-5-1a"></a>NIST 800-53 控制措施 MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>维护人员 | 不具备相应访问权限的人员

MA-5 (1).a：组织实施相应流程，以控制未接受相应安全调查或非美国公民的维护人员（包括不具备必需的访问授权、调查或正式访问批准的访问人员）在信息系统上实施维护及诊断活动时由经过完全调查、具备相应访问授权和获得技术认证的获批组织人员进行陪同和监督；在由不具备必需访问授权、调查或正式访问批准的人员执行维护或诊断活动之前，信息系统内的所有易失信息存储组件均需进行清理，且需从系统中移除所有非易失存储媒体或从物理上断开连接并进行保护。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护人员授权和陪同流程足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800-53 控制措施 MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>维护人员 | 不具备相应访问权限的人员

MA-5 (1).b：组织制定并实施一套备用安全措施来应对无法清理、移除信息系统组件或无法断开连接的情况。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统维护人员授权和陪同流程足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800-53 控制措施 MA-6

#### <a name="timely-maintenance"></a>及时维护

MA-6：组织在出现故障的[赋值：组织定义的时间段]内获取[赋值：组织定义的信息系统组件] 的维护支持和/或备件。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 数据中心内常驻维护人员，以便为关键数据中心基础结构系统和数据中心操作进行支持。 团队已明确要维护备件进行场内作业的关键安全及技术系统组件。 根据设计，关键系统采用 N+1 配置且服务可恢复。 由此，使数据中心管理团队能够在服务中断或应急计划情况下实现恢复目标。 关键信息系统服务预配为来自多个数据中心，避免由于某个数据中心出现故障而造成服务中断。 客户应用程序负责部署多个数据中心，以提供冗余和复原。 |
