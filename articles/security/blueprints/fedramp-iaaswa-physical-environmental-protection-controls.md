---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 物理保护和环境保护
description: FedRAMP Web 应用程序自动化 - 物理保护和环境保护
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206283"
---
# <a name="physical-and-environmental-protection-pe"></a>物理保护和环境保护 (PE)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-pe-1"></a>NIST 800-53 控制措施 PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>物理保护和环境保护政策与过程

**PE-1** 组织制定、记录并向 [赋值：组织定义的人员或角色] 分发一项物理保护和环境保护政策，该政策提出目标、范围、角色、职责、管理承诺、组织实体间的协调和符合性；设定用于促进实施物理保护和环境保护政策及相关的物理保护和环境保护控制措施的程序；按 [赋值：组织定义的频率] 评审并更新当前物理保护和环境保护政策并按 [赋值：组织定义的频率] 评审并更新物理保护和环境保护程序。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级物理保护和环境保护政策和程序可足以满足此项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800-53 控制措施 PE-2.a

#### <a name="physical-access-authorizations"></a>物理访问授权

**PE-2.a** 组织制定、批准并维护有权访问信息系统所在设施的人员名单。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 对 Microsoft 数据中心的物理访问，必须由数据中心管理 (DCM) 团队使用数据中心访问工具批准。 给访问分配一个结束日期，该日期后将自动删除访问，重新访问需重新获得批准。 另外，若不再需要访问，数据中心安全人员或管理人员会手动请求终止访问。 |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800-53 控制措施 PE-2.b

#### <a name="physical-access-authorizations"></a>物理访问授权

**PE-2.b** 组织颁发用于访问设施的授权凭据。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 数据中心访问工具是权威来源，列出了具有特定数据中心访问权的所有人员。 该工具与数据中心的物理安全访问控制设备相关联，并根据 DCM 团队批准的访问级别对访问进行授权。 在此工具中，将访问级别分配给用户的 Microsoft 颁发的徽章或由控件室主管 (CRS) 在数据中心分配的临时访问徽章。 访问级别由 DCM 团队批准。 除了已分配给物理徽章的凭据，数据中心的一些区域还需要注册用户的生物特征数据（掌形或指纹）。 |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800-53 控制措施 PE-2.c

#### <a name="physical-access-authorizations"></a>物理访问授权

**PE-2.c** 组织按 [赋值：组织定义的频率] 频率评审访问列表，此列表详细列出了个人的授权设施访问情况。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 除了 a 部分中描述的访问撤销之外，Microsoft Azure 还会每 90 天评审一次 Azure 数据中心的授权访问列表，从而根据需要删除/更新个别访问。 |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800-53 控制措施 PE-2.d

#### <a name="physical-access-authorizations"></a>物理访问授权

**PE-2.d** 组织将不再需要访问的个人从设施访问列表中删除。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 达到分配的访问结束日期时，Microsoft Azure 自动删除访问权限。 不再需要访问时，数据中心安全人员或管理人员将在数据中心访问工具中手动请求终止访问。 此外，Microsoft Azure 将删除任何因 c 部分中所述的访问列表评审而发现的不需要的访问授权。 |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 控制措施 PE-3.a

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.a** 组织通过在授予设施访问权限之前验证个人访问授权，以及通过使用 [选择（一项或多项）：[赋值：组织定义的物理访问控制系统/设备]；保护装置] 控制设施入口/出口，在 [赋值：组织定义的信息系统所在设施的入口点/出口点] 强制进行物理访问授权。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 使用全天候人员配备、警报、视频监控、多重身份验证和陷阱门户设备，为 Azure 数据中心的所有物理访问点强制实施物理访问授权。 |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800-53 控制措施 PE-3.b

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.b** 组织维持 [赋值：组织定义的入口点/出口点] 的物理访问审核日志。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 对 Azure 数据中心设备的所有访问都会被记录和审核。 |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800-53 控制措施 PE-3.c

#### <a name="physical-access-control"></a>物理访问控制

PE-3.c**PE-3.c** 组织提供 [赋值：组织定义的安全防护措施] 来控制对官方指定为可公开访问的设施内的区域的访问。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Azure 数据中心不包含指定为可公开访问的区域。 |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800-53 控制措施 PE-3.d

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.d** 组织根据 [赋值：组织定义的需要访客陪同和监视的情况] 陪同访客并监视访客的活动。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 所有获得批准访问数据中心的访客（见 PE-2），在他们的徽章上或通过其他视觉提示（例如彩色徽章）均被指定为“仅限陪同访问”，并且要求他们始终与陪同人员待在一起。 受陪同的访客没有被授予任何访问级别，只能根据陪同人员的访问权限进行访问。 陪同人员监视访客在数据中心进行的所有活动。 |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800-53 控制措施 PE-3.e

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.e** 组织确保钥匙、暗码锁和其他物理访问设备的安全。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 将钥匙和临时访问徽章放在安全运营中心 (SOC)，确保其安全。 安全人员全天候提供服务。 将特定人员的访问徽章与钥匙比对，将钥匙登记借给该人员。 每次轮班期间都要进行钥匙盘点，不允许将钥匙带到场外。 |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800-53 控制措施 PE-3.f

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.f** 组织按 [赋值：组织定义的频率] 的频率盘点 [赋值：组织定义的物理访问设备]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 对 Azure 数据中心内的物理访问设备至少每年进行一次盘点。 每个班次期间，一天中需要对钥匙和临时访问徽章进行多次盘点。 访问徽章读取器和类似的访问设备链接到不断显示状态的物理安全系统。 |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800-53 控制措施 PE-3.g

#### <a name="physical-access-control"></a>物理访问控制

**PE-3.g** 组织按 [分配：组织定义频率] 的频率和/或在钥匙丢失、暗码泄露或者个人调动或离职时更换暗码和钥匙。  

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 数据中心对于访问徽章或钥匙丢失或者人员离职或调动的情况设立了一些要执行的程序。 就人员离职或调动而言，会立即从系统中删除该人员的访问权限和其访问徽章。 |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800-53 控制措施 PE-3.(1)

#### <a name="physical-access-control--information-system-access"></a>物理访问控制 | 信息系统访问

**PE-3 (1)** 除了对 [赋值：包含一个或多个信息系统组件的组织定义的物理空间] 处的设施的物理访问控制外，组织还强制对信息系统进行物理访问授权。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 通过电子访问控制、生物识别设备和反传递控制等各种安全机制进一步限制 Microsoft 数据中心内包含关键系统（例如，托管、关键环境、MDF 室等）的区域。 与访问数据库中心其他区域相比，将访问 Azure 托管区域作为一种单独的级别更高的 DCAT 访问。 此外，所有 Azure 全职员工 (FTE) 和有权访问 Azure 政府托管区域的供应商都需要正式经历 Microsoft 云屏蔽和美国公民身份验证，才能获得对此环境的访问权限。 有关 Azure 政府托管区域云屏蔽的详细信息，请参阅 PS-03 部分。 |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 控制措施 PE-4

#### <a name="access-control-for-transmission-medium"></a>传输媒体的访问控制

**PE-4** 组织使用 [赋值：组织定义的安全保护措施] 控制对组织设施内 [赋值：组织定义的信息系统输配电线路] 的物理访问。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 通过设计和构建主配电网 (MDF) 室和集合来实现对传输媒体的访问控制，以保护信息系统输配电线路免受意外损坏、中断和物理篡改。 访问 MDF 室和托管区域需要双因素身份验证（访问徽章和生物识别信息）。 这确保将访问限制为仅授权人员（请参阅 PE-2、PE-3）。 在 MDF 中，使用金属导管、锁定机架、保持架或电缆盘保护输配电线路免受意外损坏、中断和物理篡改。 |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 控制措施 PE-5

#### <a name="access-control-for-output-devices"></a>输出设备的访问控制

**PE-5** 组织控制对信息系统输出设备的物理访问，以防未经授权的个人获取输出。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 数据中心没有永久连接到 Azure 资产或 Azure 共享资源的输出设备（监视器、打印机、音频设备等）。 除了不具有输出设施之外，每次轮班期间，安全人员还须实际检查设施多次，检查门是否锁好、机架是否安全稳固等事项。 仅限具有获得批准的访问授权的人员访问数据中心。 访问托管区域需要两因素身份验证（访问徽章和生物识别信息）。 |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800-53 控制措施 PE-6.a

#### <a name="monitoring-physical-access"></a>监视物理访问

**PE-6.a** 组织监视对信息系统所在设施的物理访问，以检测和响应物理安全性事件。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 通过实现数据中心内的安全设备和进程，监视物理访问。 例如，对访问控制、报警系统和视频系统的全天候电子监视，以及对设施和场地的全天候现场安全巡逻。 控制室主管始终在 SOC 中，在数据中心内监视物理访问情况。 |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800-53 控制措施 PE-6.b

#### <a name="monitoring-physical-access"></a>监视物理访问

**PE-6.b** 组织按 [赋值：组织定义频率] 的频率以及在发生 [赋值：组织定义的事件或事件的潜在迹象] 时评审物理访问日志。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 物理访问日志会得到持续的评审和维护，以供后续调查性查看。 |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800-53 控制措施 PE-6.c

#### <a name="monitoring-physical-access"></a>监视物理访问

**PE-6.c** 组织通过组织事件响应功能协调评审和调查的结果。 

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 数据中心内发生的安全事件由安全组记录。 安全小组创建报告，说明事件发生后安全事件的详细信息。 <br /> 对于需要政府通知的事件，Microsoft Azure 安全团队将与主要应用程序提供商（如 O365 ）协调，在 US-CERT 指南（参见 IR-6）中通知政府机构客户、美国 CERT 和 FedRAMP。 |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800-53 控制措施 PE-6.(1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>监视物理访问 | 入侵警报/监视设备

**PE-6 (1)** 组织监视物理入侵警报和监视设备。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 除了全天候现场安全措施外，Microsoft Azure（租用及完全托管的）数据中心还利用警报监视系统和 CCTV。 控件室主管负责在 SOC 中全天候监视和响应警报。 在响应期间，控制室主管利用被调查事件所在区域的摄像机向响应方提供实时信息。 |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800-53 控制措施 PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>监视物理访问 | 监视对信息系统的物理访问

**PE-6 (4)** 除了监视 [赋值：包含一个或多个信息系统组件的组织定义的物理空间] 处的设施的物理访问之外，组织还监视对信息系统的物理访问。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 监视对数据中心内设施和信息系统的物理访问。 所有 Microsoft 联机服务的设备被放置在数据中心，此中心内，物理访问受到监视。 所有托管区域和 MDF 室都通过访问控制、警报和视频受到保护。 |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800-53 控件 PE-8.a

#### <a name="visitor-access-records"></a>访客访问记录

**PE-8.a** 组织在 [赋值：组织定义的时间段] 内将访客访问记录保存到信息系统所在设施中。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 数据中心访问记录使用已批准的请求的形式保存在数据中心访问工具中。 如 PE-3 中所述，要求从始至终陪同访客。 将记录数据中心内的陪同访问，如果需要可以关联到访客，供将来审阅。 |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800-53 控制措施 PE-8.b

#### <a name="visitor-access-records"></a>访客访问记录

**PE-8.b** 组织按 [分配：组织定义的频率] 的频率评审访客访问记录。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 访问请求已获得批准的访客，将在验证其身份时，通过政府颁发的 ID 或 Microsoft 颁发的徽章的形式，接受访问记录评审。 如 PE-3 中所述，访客在数据中心内从始至终都需要陪同。 |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800-53 控制措施 PE-8.(1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>访客访问记录 | 自动记录维护/评审

**PE-8 (1)** 组织运用自动机制来促进对访客访问记录的维护和评审。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 采用已批准的 DCAT 请求的形式维护数据中心访问记录。 DCAT 请求仅由 DCM 团队批准。 在 DCAT 内分配和管理数据中心内的访问级别。 每季度都会对数据中心访问进行评审。 对 Azure 数据中心的所有访问都记录在 DCAT 中，可供将来调查所用。 访客从始至终都需要陪同。 将数据中心内的陪同访问记录在警报监视系统中，如果需要可以关联到访客，供将来审阅。 访客的访问会持续受到指定陪同和由控制室主管通过 CCTV 和警报监视系统的评审。 不向访客提供访问权限，访客须始终受到陪同的陪伴。 |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 控制措施 PE-9

#### <a name="power-equipment-and-cabling"></a>电源设备和布线

**PE-9** 此组织保护信息系统电源设备和电源布线免受损坏和破坏。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 提供保护空间和适当的电缆标记。 Microsoft Azure 基础结构设备（例如电缆、电线和备用发电机）须放置在经过设计的环境中，使其免受环境风险，例如盗窃、火灾、爆炸、烟雾、水、灰尘、振动、地震、有害化学物、电气干扰、断电、电气干扰（峰值）等。 所有便携式联机服务资产（例如机架、服务器、网络设备）均须锁定或固定在某个地方，以防止盗窃或因移动而损坏。 在 Microsoft Azure 任何环境中的电力和信息系统电缆均已得到适当标记和保护，以防被截获或损坏。 电力和信息系统电缆在环境中的各点间相互隔离，以避免干扰。 |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800-53 控制措施 PE-10.a

#### <a name="emergency-shutoff"></a>紧急关闭

**PE-10.a** 组织提供在紧急情况下关闭信息系统或单个系统组件的电源的功能。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 按本地消防规范的要求，在数据中心内安装了紧急断电 (EPO) 按钮。 在某些 Microsoft Azure 托管的数据中心内，数据中心设计不需要 EPO 按钮。 |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800-53 控件 PE-10.b

#### <a name="emergency-shutoff"></a>紧急关闭

PE-10.b 组织将紧急关闭开关或设备放置在 [赋值：组织定义的信息系统或系统组件位置]，以便人员安全轻松地访问。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 巧妙地设定 EPO 按钮的位置，便于在紧急情况下激活。 EPO 按钮可放置在托管区域中，或有人操作的设施操作中心 (FOCs) 中，也可按照当地消防规范的要求进行放置。 |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800-53 控制措施 PE-10.c

#### <a name="emergency-shutoff"></a>紧急关闭

PE-10.c 组织防止紧急断电功能受到未经授权的激活。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 为防止意外激活，EPO 按钮可能有一个保护壳，需要双重激活，或在激活之前通过声音警报发出警告。 此外，EPO 按钮受视频监控。 |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 控制措施 PE-11

#### <a name="emergency-power"></a>应急电源

**PE-11** 组织提供短期不间断电源，用于在主电源丢失时帮助 [选择 （一项或多项）：有序地关闭信息系统；将信息系统转换到长期备用电源]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 通过使用不间断电源 (UPS) 系统（此系统提供短期供电，可在发电机开始工作之前提供电源）保护数据中心设备和电路，从而利用应急电源。 |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800-53 控制措施 PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>应急电源 | 长期备用电源 - 最低运作能力

**PE-11 (1)** 组织为信息系统提供一个长期备用电源，能够在主电源长时间丢失的情况下维持所需的最低运作能力。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 已为信息系统实现了一个长期备用电源，能够在主电源长时间丢失的情况下保持最低运作能力。 当电源出现故障或降至不可接受的电压水平时，不间断电源 (UPS) 系统会立即启动并接管电源负载。 这可在发电机接管电源之前为运行服务器提供足够的电源。 应急发电机提供备用电源以备长时间的停电和进行计划维护，并且在发生自然灾害时可以使用现场动力储备运行数据中心。 Azure 在许多数据中心都有柴油发电机。 在有必要帮助维持电网稳定、在非常修理以及在需要将数据中心脱离电网的维修情况下，可以使用备用发电机。 |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800-53 控制措施 PE-12

#### <a name="emergency-lighting"></a>应急照明

**PE-12** 组织为信息系统采用和维护自动应急照明，应急照明在发生电源故障或断电时激活，涵盖设施内的紧急出口和疏散路线。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure （租用且完全托管的）数据中心在由 UPS 和发电机系统（请参阅 PE-11）备份的专用线路上以顶置应急照明的形式实现应急照明。 按照美国消防协会 (NFPA) 生命安全法规的要求，沿所有疏散路线、紧急出口以及在托管区域内部实现了自动应急照明。 如果公用电源断电，应急照明将自动切换到 UPS 和发电机系统提供的电源。 在 Microsoft Azure 数据中心内的应急照明系统将进行日常维护，以确保其保持正常的工作状态。 |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800-53 控制措施 PE-13

#### <a name="fire-protection"></a>消防

**PE-13** 组织使用和维护由独立能源支持的信息系统的灭火和检测设备/系统。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 已通过在 Microsoft Azure 数据中心安装火灾检测和灭火系统实现消防措施。 <br /> Microsoft Azure 数据中心实现可靠的火灾检测机制。 Microsoft Azure 消防措施包括在与消防喷淋系统集成的地板下和天花板上安装光电感烟探测器。 此外，在每个托管区域中还有 Xtralis VESDA（极早期烟雾探测设备）系统，用于监视空气状况。 VESDA 装置是安装在多个高价值空间的高灵敏度空气采样系统。 它可以在实际火灾检测警报发生之前，进行调查响应。 <br /> 在整个数据中心内安装了多个“拉式火警箱”火灾报警器，用于手动触发火灾报警通知。 灭火器遍布整个数据中心，每年都要进行适当的检查、维修和标记。 每个班次期间，安保人员会在所有的建筑区域进行多次巡逻。 数据中心人员每天都会现场演练，确保满足所有消防检查要求。 <br /> 含有敏感电气设备的区域（托管、MDF 等）由双联锁预作用（干式）喷水灭火系统保护。 干式灭火器是一个两级预作用系统，需要喷头激活（由于热量），且需要烟雾探测用于放水。 喷头的激活释放管内气压，使管道填满水。 当烟雾或热量检测器也被激活时，便会释放出水。 <br /> 火灾检测/灭火和应急照明系统都连接到用作备用电源的数据中心 UPS 和发电机系统。 |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800-53 控制措施 PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>消防 | 检测设备/系统

**PE-13 (1)** 组织为信息系统应用在发生火灾时自动激活并通知 [赋值：组织定义的人员或角色] 和 [赋值：组织定义的应急响应方] 的火灾检测设备/系统。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 组织为信息系统应用在发生火灾时自动激活并通知数据中心人员和应急响应方的火灾检测设备/系统。 如果某一个火灾检测机制在任何托管区域中激活，则会通过消防警报系统自动通知当地消防部门。 此外，消防和火灾探测系统会被并入安全系统，通知当地的设施和安保人员。 |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800-53 控制措施 PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>消防 | 灭火设备/系统

**PE-13 (2)** 组织为信息系统应用在出现任何火灾情况时自动通知 [赋值：组织定义的人员或角色] 和 [赋值：组织定义的应急响应方] 的灭火设备/系统。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 如果在数据中心激活了某一个灭火系统，则会通过消防警报系统自动通知当地消防部门。 此外，消防和火灾探测系统会被并入安全系统，通知当地的设施和安保人员。 |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800-53 控制措施 PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>消防 | 自动灭火

**PE-13 (3)** 当设施没有全天候配备人员时，组织会使用信息系统的自动灭火功能。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 数据中心全天候提供服务。 检测到火灾报警情况时，灭火系统会自动启动，无需人工干预。 |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800-53 控制措施 PE-14.a

#### <a name="temperature-and-humidity-controls"></a>温度和湿度控制

**PE-14.a** 组织按照 [分配：组织定义的可接受级别] 维护信息系统所在设施内的温度和湿度水平。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 根据美国采暖、制冷和空调工程师学会 (ASHRAE) 的指导方针，维持温度和湿度水平。 数据中心的楼宇管理系统 (BMS) 持续监视温度和湿度水平。 |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800-53 控件 PE-14.b

#### <a name="temperature-and-humidity-controls"></a>温度和湿度控制

**PE-14.b** 组织按 [分配：组织定义的频率] 的频率监视温度和湿度水平。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 在 Microsoft Azure 数据中心，建筑管理系统 (BMS) 持续监视温度和湿度水平。 数据中心人员从设施操作中心 (FOC) 监视 BMS，因此，他们能在超过任何警报点之前管理数据中心内的温度和湿度。 |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800-53 控制措施 PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>温度和湿度控制 | 监视警报/通知

**PE-14 (2)** 组织采用温度和湿度监视，提供对人员或设备有潜在危害的变化的警报或通知。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 在 Microsoft Azure 数据中心，建筑管理系统 (BMS) 持续监视温度和湿度水平。 数据中心人员从设施操作中心 (FOC) 监视 BMS，因此，他们能在超过任何警报点之前管理数据中心内的温度和湿度。 |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800-53 控制措施 PE-15

#### <a name="water-damage-protection"></a>水害保护

**PE-15** 组织通过提供主要关闭或隔离阀来保护信息系统免受漏水造成的损害，这些阀易使用、工作正常并且为关键人员所知。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 为有漏水风险的区域（如空气处理器单元）提供水位/泄漏检测。 灭火系统还监视泄漏检测警报。 水位/泄漏检测系统与设施警报和通知系统相集成。 数据中心中的喷水灭火系统进行了分区。 数据中心人员熟悉需要使用关水阀的应急程序以及这些阀的位置。 可通过闸阀将喷水灭火系统主管单个或作为一组关闭。 在关键空间内的所有喷水灭火器都是双联锁预作用型喷水灭火器，在启动水流之前需要两种激活形式。 喷水灭火系统的压力受到监视，会针对漏水发出警报。 |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800-53 控制措施 PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>水害保护 | 自动化支持

**PE-15 (1)** 组织采用自动机制来检测信息系统附近的水，并通知 [赋值：组织定义的人员或角色]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 采用自动机制来检测数据中心是否有水，并向数据中心人员发出警报。 Azure 为有漏水风险的区域（如空气处理器单元）提供水位/泄漏检测。 灭火系统还监视泄漏检测警报。 水位/泄漏检测系统与设施警报和通知系统相集成。 喷水灭火系统的压力受到监视，会针对漏水发出警报。 |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800-53 控制措施 PE-16

#### <a name="delivery-and-removal"></a>传递和删除

**PE-16** 组织授权、监视和控制接入和退出设施的 [分配：组织定义的信息系统组件类型]，并维护这些项目的记录。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 代表客户实现此要求。 Microsoft Azure 严格执行可接入和退出数据中心的项目。 在资产管理工具数据库中，所有系统组件/资产都受到跟踪。 |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800-53 控制措施 PE-17.a

#### <a name="alternate-work-site"></a>备用工作站点

**PE-17.a** 组织在备用工作站点使用 [分配：组织定义的安全控件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级备用工作站点规定可满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pe-17b"></a>NIST 800-53 控制措施 PE-17.b

#### <a name="alternate-work-site"></a>备用工作站点

**PE-17.b** 组织评估在备用工作站点的安全控制措施是否可行且有效。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级备用工作站点规定可满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 控制措施 PE-17.c

#### <a name="alternate-work-site"></a>备用工作站点

**PE-17.c** 组织在出现安全事件或问题的情况下为员工提供了与信息安全人员沟通的方式。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级备用工作站点规定可满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-pe-18"></a>NIST 800-53 控制措施 PE-18

#### <a name="location-of-information-system-components"></a>信息系统组件的位置

**PE-18** 组织将信息系统组件放在设施内的，尽量减少 [分配：组织定义的物理和环境危害] 的潜在危害，并尽量减少未经授权的访问机会。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户对 Azure 数据中心的任何系统资源均不享有物理访问权限。 |
| **提供商 (Microsoft Azure)** | Azure 实现战略性数据中心设计方法，以满足信息系统组件控制的位置需要。 所有 Microsoft 联机服务的设备都放置在经过设计的位置，以保护其免受环境风险，例如盗窃、火灾、爆炸、烟雾、水、灰尘、振动、地震、有害化学物、电气干扰、停电、电气干扰（峰值）和辐射等。 该设施和基础结构已实现抗震支撑以防止环境危害。 所有托管区域和 MDF 室都通过访问控制、警报和视频受到保护。 该设施还由安全人员全天候巡查。 所有便携式 Azure 资产都须锁定或固定在某个地方，以防止盗窃或因移动造成损坏。 |


