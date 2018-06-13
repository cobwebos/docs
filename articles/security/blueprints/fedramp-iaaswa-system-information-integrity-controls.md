---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 系统和信息完整性
description: FedRAMP Web 应用程序自动化 - 系统和信息完整性
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206282"
---
# <a name="system-and-information-integrity-si"></a>系统和信息完整性 (SI)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-si-1"></a>NIST 800-53 控制 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>系统和信息完整性策略和过程

SI-1 组织制定、记录系统和信息完整性策略，并将其分发给[分配：组织定义的人员或角色]，以解决用途、范围、角色、职责、管理承诺、组织实体之间的协调和合规性；促进系统和信息完整性策略及其关联的系统和信息完整性控制的实施过程；审阅和更新当前系统和信息完整性策略[分配：组织定义的频率]；以及系统和信息完整性过程[分配：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统和信息完整性策略及过程应能够足以应对此控制措施。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 控制 SI-2.a

#### <a name="flaw-remediation"></a>缺陷修正

SI-2.a 组织识别、报告及更正信息系统缺陷。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署自动化和控制解决方案，以跟踪在此体系结构中部署的 Windows 虚拟机的更新状态。 在仪表板中，“更新管理”磁贴显示所有已部署的 Windows 服务器的缺陷修正状态。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 控制 SI-2.b

#### <a name="flaw-remediation"></a>缺陷修正

SI-2.b 组织在安装与缺陷修正相关的软件和硬件更新之前测试其有效性和潜在副作用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 在客户部署的资源上安装与缺陷修正相关的更新之前，客户负责测试其有效性和潜在副作用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 控制 SI-2.c

#### <a name="flaw-remediation"></a>缺陷修正

SI-2.c 组织在更新发布的[分配：组织定义的时间段]内安装与安全相关的软件和硬件更新。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的 Windows 虚拟机默认配置为从 Windows 更新服务接收自动更新。 另外，此解决方案还部署自动化和控制解决方案，通过此方案可以创建更新部署，以便在需要时将修补程序部署到 Windows 服务器上。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 控制 SI-2.d

#### <a name="flaw-remediation"></a>缺陷修正

SI-2.d 组织将缺陷修正合并到组织的配置管理过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责将缺陷修正纳入到配置管理中。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 控制 SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>缺陷修正 | 集中管理

SI-2 (1) 组织集中管理缺陷修正过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署自动化和控制解决方案，以跟踪在此体系结构中部署的 Windows 虚拟机的更新状态。 在仪表板中，“更新管理”磁贴显示所有已部署的 Windows 服务器的缺陷修正状态。 可以创建更新部署，以便在需要时将修补程序部署到 Windows 服务器上。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 控制 SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>缺陷修正 | 自动缺陷修正状态

SI-2 (2) 组织采用自动机制[分配：组织定义的频率]来确定缺陷修正方面的信息系统组件状态。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署自动化和控制解决方案，以跟踪在此体系结构中部署的 Windows 虚拟机的更新状态。 对于每台托管的 Windows 计算机，每天执行两次扫描。 每隔 15 分钟就会调用一次 Windows API，以便查询上次更新时间，从而确定状态是否已更改，并在状态已更改的情况下启动符合性扫描。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 控制 SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>缺陷修正 | 修正缺陷的时间 / 纠正措施的基准

SI-2 (3).a 组织判断缺陷识别和缺陷修正之间的时间。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责修正客户部署资源内的缺陷。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 控制 SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>缺陷修正 | 修正缺陷的时间 / 纠正措施的基准

SI-2 (3).b 组织建立[分配：组织定义的基准]来采取纠正措施。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖于缺陷修正过程的企业级基准。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800-53 控制 SI-3.a

#### <a name="malicious-code-protection"></a>恶意代码防护

SI-3.a 组织在信息系统入口和出口点使用恶意代码防护机制来检测和根除恶意代码。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800-53 控制 SI-3.b

#### <a name="malicious-code-protection"></a>恶意代码防护

SI-3.b 在有新版本发布时，组织根据组织配置管理策略和过程来更新恶意代码防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 该扩展配置为有新版本发布时自动更新反恶意软件引擎和保护签名。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800-53 控制 SI-3.c

#### <a name="malicious-code-protection"></a>恶意代码防护

SI-3.c 组织配置恶意代码防护机制，以定期扫描信息系统[分配：组织定义频率]，并根据组织的安全策略，在下载、打开、执行来自外部源的文件时，在[选择（一个或更多）；终结点；网络入口/出口点]实时扫描文件；[选择（一个或多个）：阻止恶意代码；隔离恶意代码；向管理员发送警报；[分配：组织定义的操作]]来响应恶意代码检测。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 配置此扩展，以执行实时和定期扫描（每周）、自动更新反恶意软件引擎和保护签名，以及执行自动修正操作。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 控制 SI-3.d

#### <a name="malicious-code-protection"></a>恶意代码防护

SI-3.d 组织处理在恶意代码检测和根除期间收到的误报，并处理对信息系统可用性产生的潜在影响。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责防范恶意代码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 控制 SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>恶意代码防护 | 集中管理

SI-3 (1) 组织集中管理恶意代码防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 Azure 提供了一种集中式功能来查看反恶意软件解决方案的当前状态。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 控制 SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>恶意代码防护 | 自动更新

SI-3 (2) 信息系统会自动更新恶意代码防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 该扩展配置为有新版本发布时自动更新反恶意软件引擎和保护签名。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 控制 SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>恶意代码防护 | 基于非签名的检测

SI-3 (7) 信息系统实现基于非签名的恶意代码检测机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图针对所有部署的 Windows 虚拟机部署基于主机的反恶意软件保护，通过 Microsoft 反恶意软件虚拟机扩展实现。 此扩展插件配置为执行启发式检测。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 控制 SI-4.a

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.a 组织根据[分配：组织定义的监视目标]监视信息系统，以检测攻击和潜在攻击指示器；以及未经授权的本地、网络和远程连接。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Log Analytics 以及安全和审核解决方案。 此解决方案提供有关安全状况、攻击和潜在攻击指示器的综合视图。 “安全和审核”仪表板使用所部署管理解决方案中的可用数据，提供对已部署资源的安全状态的高级见解。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 控制 SI-4.b

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.b 组织通过[分配：组织定义的技术和方法]来识别未经授权使用信息系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署安全和审核解决方案。 标识和访问提域供了一个仪表板，其中包含信息系统标识状态的概述，包括登录失败的尝试次数和当前登录的帐户数。 此仪表板上的提供的信息可以帮助识别潜在的可疑活动。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 控制 SI-4.c

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.c 组织在信息系统中战略性地部署监视设备，以收集组织确定的重要信息；在系统内的特定位置，跟踪组织感兴趣的特定类型的事务。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Log Analytics 以及安全和审核解决方案。 安全和审核仪表板使用已部署的管理解决方案中的可用数据，提供对部署资源的安全状态的高级见解，包括对 VM 操作系统监视数据的见解。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 控制 SI-4.d

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.d 组织保护从入侵监视工具中获得的信息，避免未经授权的访问、修改和删除。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 逻辑访问控制用于防止此蓝图内的监视信息遭到未经授权的访问、修改和删除。 Azure Active Directory 使用基于角色的组成员身份强制执行已批准的逻辑访问。 查看监视信息和使用监视工具的能力仅限于需要这些权限的用户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800-53 控制 SI-4.e

#### <a name="information-system-monitoring"></a>信息系统监视

**SI-4.e** 根据执法信息、情报信息或其他可靠的信息来源，当指示组织运营和资产、个人、其他组织或国家所面临的风险增加时，组织负责提高信息系统监视活动级别。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 控制 SI-4.f

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.f 组织根据适用的联邦法律、行政命令、指令、政策或法规，获取有关信息系统监视活动的法律意见。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 控制 SI-4.g

#### <a name="information-system-monitoring"></a>信息系统监视

SI-4.g 组织向[分配：组织定义的人员或角色]提供[分配：组织定义的信息系统监视信息]；[选择（一个或多个）：根据需要；[分配：组织定义的频率]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 控制 SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>信息系统监视 | 系统范围的入侵检测系统

SI-4 (1) 组织将各个入侵检测工具连接并配置到信息系统范围的入侵检测系统。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 控制 SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>信息系统监视 | 用于实时分析的自动化工具

SI-4 (2) 组织使用自动化工具来支持准实时事件分析。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Log Analytics 以及各种管理解决方案，包括安全和审核解决方案。 Log Analytics 在已部署的资源中提供准实时事件分析。 管理解决方案提供跨解决方案域的安全状况的综合视图。 Log Analytics 使用已部署的管理解决方案中的可用数据，提供对部署资源的安全状态的见解。 可以将 Log Analytics 配置为根据已定义的标准生成警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 控制 SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>信息系统监视 | 入站和出站通信流量

SI-4 (4) 信息系统监视入站和出站通信流量[分配：组织定义的频率]来发现异常或未经授权的活动或条件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 控制 SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>信息系统监视 | 系统生成的警报

SI-4 (5) 当出现以下泄露或潜在泄露迹象时，信息系统会向[分配：组织定义的人员或角色]发出警报：[分配：组织定义的泄露指示器]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署各种管理解决方案，包括安全和审核解决方案。 Log Analytics 在已部署的资源中提供准实时事件分析。 管理解决方案提供跨解决方案域的安全状况的综合视图。 可以将 Log Analytics 配置为根据已定义的标准生成警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 控制 SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>信息系统监视 | 分析通信流量异常

SI-4 (11) 组织分析信息系统外部边界上的出站通信流量，并选择[分配：系统内组织定义的点（例如，子网、子系统）]以发现异常。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分析客户部署资源的通信流量异常。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 控制 SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>信息系统监视 | 无线入侵检测

SI-4 (14) 组织使用无线入侵检测系统来识别恶意无线设备，并检测对信息系统的攻击尝试和潜在泄露/破坏。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 数据中心，不允许使用包括无线设备在内的客户控制的硬件。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 按季度定期对恶意无线信号进行监视，如 AC-18 中所述。 <br /> Microsoft Azure 代表 PaaS 和 IaaS 客户实现此控制措施。 |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 控制 SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>信息系统监视 | 关联监视信息

SI-4 (16) 组织将整个信息系统中使用的监视工具中的信息关联起来。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Log Analytics 以及各种管理解决方案，包括安全和审核解决方案。 Log Analytics 使用已部署的管理解决方案中的可用数据，提供对部署资源的安全状态的见解。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 控制 SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>信息系统监视 | 分析流量 / 隐蔽渗透

SI-4 (18) 组织分析信息系统外部边界（即系统外围）和[分配：在系统内组织定义的内部点（例如子系统、子网络）]的出站通信流量，以检测信息的隐蔽渗透。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分析客户部署资源的通信流量。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 控制 SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>信息系统监视 | 个人带来的更大风险

SI-4 (19) 组织会对[分配：组织定义的源]标识为会带来更高级别风险的个人执行[分配：组织定义的附加监视]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视会带来更大风险的个人。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 控制 SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>信息系统监视 | 特权用户

SI-4 (20) 组织对特权用户执行[分配：组织定义的附加监视]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责监视特权用户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 控制 SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>信息系统监视 | 未经授权的网络服务

SI-4 (22) 信息系统检测未经[分配：组织定义的授权或审批过程]和[选择（一个或多个）：审核；警报[分配：组织定义的人员或角色]]授权或批准的网络服务。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责检测未经授权的网络服务。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 控制 SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>信息系统监视 | 基于主机的设备

SI-4 (23) 组织在[分配：组织定义的信息系统组件]上实现[分配：组织定义的基于主机的监视机制]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图从已部署的资源收集监视数据，包括基于主机的监视功能中的数据。 Microsoft Monitoring Agent 安装在所有 Windows 虚拟机上，以收集 Log Analytics 和其他管理解决方案使用的监视数据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 控制 SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>信息系统监视 | 泄露指示器

SI-4 (24) 信息系统发现、收集、分发和使用泄露指示器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责发现、收集、分发和使用针对客户部署资源的泄露指示器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 控制 SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>安全警报、公告和指令

SI-5.a 组织从[分配：组织定义的外部组织]持续接收信息系统安全警报、公告和指令。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户部署资源的安全警报、公告和指令（包括应用程序、操作系统、数据库和软件）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 控制 SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>安全警报、公告和指令

SI-5.b 组织在必要时生成内部安全警报、公告和指令。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户部署资源的安全警报、公告和指令。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 控制 SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>安全警报、公告和指令

SI-5.c 组织将安全警报、公告和指令分发给：[选择（一个或多个）：[分配：组织定义的人员或角色]；[分配：组织在组织内部定义的元素]；[分配：组织定义的外部组织]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户部署资源的安全警报、公告和指令。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 控制 SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>安全警报、公告和指令

SI-5.d 组织根据既定期限实施安全指令，或通知颁发组织不合规的程度。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户部署资源的安全警报、公告和指令。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 控制 SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>安全警报、公告和指令 | 自动警报和公告

SI 5-(1) 组织使用自动化机制在整个组织内提供安全警报和公告信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户部署资源的安全警报、公告和指令。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 控制 SI-6.a

#### <a name="security-function-verification"></a>安全功能验证

SI-6.a 信息系统验证[分配：组织定义的安全功能]的正确操作。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责对客户部署的资源（包括应用程序、操作系统、数据库和软件）进行安全功能验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 控制 SI-6.b

#### <a name="security-function-verification"></a>安全功能验证

SI-6.b 信息系统执行此验证[选择（一个或多个）：[分配：组织定义的系统过渡状态]；根据具有相应权限的用户发出的命令；[分配：组织定义的频率]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责客户部署资源的安全功能验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 控制 SI-6.c

#### <a name="security-function-verification"></a>安全功能验证

SI-6.c 信息系统通知[分配：组织定义的人员或角色]有关失败的安全验证测试。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责客户部署资源的安全功能验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 控制 SI-6.d

#### <a name="security-function-verification"></a>安全功能验证

SI-6.d 发现异常时，信息系统[选择（一个或多个）：关闭信息系统；重新启动信息系统；[分配：组织定义的备用操作]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责客户部署资源的安全功能验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800-53 控制 SI-7

#### <a name="software-firmware-and-information-integrity"></a>软件、固件和信息完整性

SI-7 组织采用完整性验证工具来检测对[分配：组织定义的软件、固件和信息]未经授权的更改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 提供实时文件完整性验证、保护和恢复核心系统文件，这些文件通过 Windows 资源保护 (WRP) 功能作为 Windows 或授权 Windows 系统更新的一部分安装。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 控制 SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>软件、固件和信息完整性| 完整性检查

SI-7 (1) 信息系统执行[分配：组织定义的软件、固件和信息]的完整性检查[选择（一个或多个）：在启动时；在[分配：组织定义的过渡状态或安全相关事件]中；[分配：组织定义的频率]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 提供实时文件完整性验证、保护和恢复核心系统文件，这些文件通过 Windows 资源保护 (WRP) 功能作为 Windows 或授权 Windows 系统更新的一部分安装。 WRP 启用实时完整性检查。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 控制 SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>软件、固件和信息完整性 | 自动通知完整性冲突

SI-7 (2) 组织使用自动化工具，在完整性验证期间发现差异时向[分配：组织定义的人员或角色]提供通知。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 提供实时文件完整性验证、保护和恢复核心系统文件，这些文件通过 Windows 资源保护 (WRP) 功能作为 Windows 或授权 Windows 系统更新的一部分安装。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 控制 SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>软件、固件和信息完整性 | 自动响应完整性冲突

SI-7 (5) 发现完整性冲突时，信息系统会自动[选择（一个或多个）：关闭信息系统；重新启动信息系统；实现[分配：组织定义的安全保护机制]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责自动响应客户部署资源内的完整性冲突。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 控制 SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>软件、固件和信息完整性 | 检测和响应的集成

SI-7 (7) 组织将未经授权的[分配：组织定义的与安全相关的信息系统更改]检测合并到组织的事件响应功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责保护客户部署资源的软件和信息的完整性。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 控制 SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>软件、固件和信息完整性 | 二进制或计算机可执行代码

SI-7 (14) 组织禁止在担保受限或无担保以及未预配源代码的情况下，使用来自源的二进制或计算机可执行代码；并且仅针对令人信服的任务/操作要求和有授权官方批准的情况下，向源代码要求提供例外情况。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统和信息完整性过程可能需要建立多项要求，以便从某些源中获取二进制或计算机可执行代码的源代码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800-53 控制 SI-8.a

#### <a name="spam-protection"></a>垃圾邮件防护

SI-8.a 组织在信息系统入口和出口点采用垃圾邮件防护机制，以检测未经请求的消息并对其执行相应操作。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署邮件服务器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 控制 SI-8.b

#### <a name="spam-protection"></a>垃圾邮件防护

SI-8.b 组织会在有新版本发布时，根据组织的配置管理策略和过程来更新垃圾邮件防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署邮件服务器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 控制 SI-8 (1)

#### <a name="spam-protection--central-management"></a>垃圾邮件防护 | 集中管理

SI-8 (1) 组织集中管理垃圾邮件防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署邮件服务器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 控制 SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>垃圾邮件防护 | 自动更新

SI-8 (2) 信息系统自动更新垃圾邮件防护机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署邮件服务器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800-53 控制 SI-10

#### <a name="information-input-validation"></a>信息输入验证

SI-10 信息系统检查[分配：组织定义的信息输入]的有效性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责客户部署资源（包括应用程序、操作系统、数据库和软件）的信息输入验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800-53 控制 SI-11.a

#### <a name="error-handling"></a>错误处理

SI-11.a 信息系统会生成错误消息，以为采取纠正措施提供所需的信息，而不会泄露可能被攻击者利用的信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的资源采用商用操作系统和软件应用程序。 此软件使用行业最佳做法，确保敏感信息不会在错误消息中显示。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800-53 控制 SI-11.b

#### <a name="error-handling"></a>错误处理

SI-11.b 信息系统仅对[分配：组织定义的人员或角色]显示错误消息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的资源采用商用操作系统和软件应用程序。 此软件使用行业最佳做法来提供在接收消息的使用上下文中适当的错误消息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800-53 控制 SI-12

#### <a name="information-handling-and-retention"></a>信息处理和保留

SI-12 组织根据适用的联邦法律、行政命令、指令、政策、法规、标准和操作要求，处理和保留信息系统内部的信息以及系统输出的信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责处理和保留客户部署资源（包括应用程序、操作系统、数据库和软件）内的信息，以及从这些资源中输出的信息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800-53 控制 SI-16

#### <a name="memory-protection"></a>内存保护

SI-16 信息系统实现[分配：组织定义的安全保护机制]，以防止对内存进行未经授权的代码执行。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 提供了以下保护来防止在受限制的内存位置执行代码：无执行 (NX)、地址空间布局随机化 (ASLR) 和数据执行保护 (DEP)。 |
| **提供商 (Microsoft Azure)** | 不适用 |
