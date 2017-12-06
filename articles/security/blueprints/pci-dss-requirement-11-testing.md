---
title: "Azure 付款处理蓝图 - 测试要求"
description: "PCI DSS 要求 11"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的测试要求 
## <a name="pci-dss-requirement-11"></a>PCI DSS 要求 11

**定期测试安全系统和进程**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

恶意攻击者和研究者会持续发现各种漏洞，而新软件则会引入这些漏洞。 应频繁测试系统组件、进程和自定义软件，确保始终根据不断变化的环境进行安全控制。

## <a name="pci-dss-requirement-111"></a>PCI DSS 要求 11.1

**11.1** 通过适当的流程测试是否存在无线接入点 (802.11)，按季检测和确定所有授权的和非授权的无线接入点。

> [!NOTE]
> 可以在流程中使用的方法包括但不限于：无线网络扫描、对系统组件和基础结构进行的物理/逻辑检查、网络访问控制 (NAC) 或无线 IDS/IPS。
不管使用哪些方法，必须确保其足以检测和确定授权设备和非授权设备。


**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Azure 不允许在 Azure 网络环境中进行无线连接。 内部安全团队定期扫描（按季）恶意无线信号，并在进行调查后将其去除。 客户不得在 Azure 环境中部署无线技术。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 解决方案中不实施无线和 SNMP 技术。|



### <a name="pci-dss-requirement-1111"></a>PCI DSS 要求 11.1.1

**11.1.1** 保留一个清单，记录授权的无线接入点，包括对业务理由进行记录。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 11.1](#pci-dss-requirement-11-1) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 解决方案中不实施无线和 SNMP 技术。|



### <a name="pci-dss-requirement-1112"></a>PCI DSS 要求 11.1.2

**11.1.2** 在检测到非授权无线接入点时实施事件响应过程。


**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 11.1](#pci-dss-requirement-11-1) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 解决方案中不实施无线和 SNMP 技术。|



## <a name="pci-dss-requirement-112"></a>PCI DSS 要求 11.2

**11.2** 至少按季运行内部和外部网络漏洞扫描，对网络进行重大更改（例如新系统组件安装、网络拓扑更改、防火墙规则修改、产品升级）后也应进行此类扫描。 

> [!NOTE]
> 为了表明所有系统都已扫描且所有相应的漏洞都已处理，可将多个按季扫描报告组合在一起。 可能需要其他记录来表明目前没有在处理尚未修正的漏洞。
> 初次确定是否符合 PCI DSS 时，如果评估师确认符合以下条件，则不要求进行四个季度的扫描且每次扫描都通过：1) 最近一次扫描结果为通过；2) 相关实体已记录要求进行按季扫描的策略和过程；3) 再次扫描表明，在以前的扫描结果中标出的漏洞已修正。 进行初次 PCI DSS 审核以后，后续的年份必须每年进行四次按季扫描，且每次扫描的结果必须为通过。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Azure 按季进行内部和外部漏洞扫描。 扫描由合格人员执行。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 已进行渗透测试并按“现状”进行漏洞扫描。 渗透测试结果可以重复，只需使用 nmap、pentest-tools.com 等常用工具即可。可以通过渗透测试结果了解攻击面（非结论性），确保没有可利用的漏洞。 另外，[Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)会提供漏洞信息和修正措施。|



### <a name="pci-dss-requirement-1121"></a>PCI DSS 要求 11.2.1

**11.2.1** 按季进行内部漏洞扫描。 解决漏洞并反复进行扫描，根据实体的漏洞严重程度（参见“要求 6.1”）验证所有“高风险”漏洞是否都已解决。 扫描必须由合格人员执行。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 对范围内的底层基础结构进行漏洞扫描。 Microsoft Azure 使用适当的漏洞扫描工具，对服务器操作系统、数据库和网络设备实施漏洞扫描。 Azure Web 应用程序使用适当的行业扫描解决方案进行扫描。 按季度进行漏洞扫描。<br /><br />根据需要对所有系统反复扫描，直到所有“高风险”漏洞（参见“要求 6.1”）都已解决。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 已进行渗透测试并按“现状”进行漏洞扫描。 渗透测试结果可以重复，只需使用 nmap、pentest-tools.com 等常用工具即可。可以通过渗透测试结果了解攻击面（非结论性），确保没有可利用的漏洞。 另外，[Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)会提供漏洞信息和修正措施。|



### <a name="pci-dss-requirement-1122"></a>PCI DSS 要求 11.2.2

**11.2.2** 通过支付卡行业安全标准委员会 (PCI SSC) 核准的核准扫描供应商 (ASV) 按季进行外部漏洞扫描。 根据需要反复扫描，直至扫描结果为通过。 

> [!NOTE]
> 按季外部漏洞扫描必须由支付卡行业安全标准委员会 (PCI SSC) 核准的核准扫描供应商 (ASV) 执行。
> 请参阅在 PCI SSC 网站上发布的“ASV 计划指南”，了解扫描客户责任、扫描准备，等等。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 对范围内可以从外部访问的底层基础结构进行外部漏洞扫描。 扫描由核准扫描供应商 (ASV) 执行。<br /><br />Microsoft Azure 订阅了 MSRC/OSSC 的按月修补程序通知，至少按季进行漏洞扫描。 已确定的漏洞会根据风险级别按既定的时间线进行评估和修正。<br /><br />每个季度都会对 Microsoft Azure 环境的优先化组件有针对地进行综合性安全漏洞扫描，以便确定安全漏洞。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 部署 Contoso Webstore 时，演示版客户负责使用经支付卡行业安全标准委员会核准的核准扫描供应商 (ASV)，根据需要对其持卡人数据环境 (CDE) 中的所有 PaaS 实例执行按季外部漏洞扫描和反复扫描。<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS 要求 11.2.3

**11.2.3** 在进行重大更改后，根据需要执行内外部扫描和反复扫描。 扫描必须由合格人员执行。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 结果会报告给利益干系人，修正措施由 Azure 安全团队在关闭案例时进行跟踪。 Azure 测试结果可能会根据 NDA（Non Disclosure Agreement，保密协议）与客户共享。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责根据需要按季对其 CDE 中的所有 PaaS 实例执行内外部漏洞扫描和反复扫描。 在进行重大更改后，应在范围内的环境中执行扫描。<br /><br />必须由 ASV 或具有组织独立性的人员执行扫描。|



## <a name="pci-dss-requirement-113"></a>PCI DSS 要求 11.3

**11.3** 实施适用于渗透测试的方法，其中包括以下内容：
- 基于行业接受的渗透测试方法（例如，NIST SP800-115）
- 包括对整个 CDE 外围和关键系统的覆盖
- 包括从网络内部和外部进行的测试
- 包括用于验证任何分段和范围缩减控件的测试
- 定义应用程序层渗透测试，使之至少包括“要求 6.5”中列出的漏洞
- 定义网络层渗透测试，使之包括支持网络功能和操作系统的组件
- 包括对过去 12 个月中遇到的威胁和漏洞的审核和考虑
- 指定渗透测试结果和修正活动结果的保留期

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 使用第三方渗透测试来验证服务，该测试基于使用 CREST 认证测试人员进行的 OWASP（开放式 Web 应用程序安全项目）的前十个漏洞。 测试结果通过风险登记簿进行跟踪，该登记簿会定期进行审核和查看，确保符合安全做法。 <br /><br />Microsoft 还对 Microsoft 托管的基础结构、服务和应用程序使用红队研判。 在红队研判和实时站点渗透测试过程中，不会特意针对最终客户数据。 测试针对 Microsoft Azure 基础结构和平台以及 Microsoft 自己的应用程序和数据。 从不针对 Azure 中托管的客户租户、应用程序和数据进行测试。<br /><br />Microsoft Azure 雇佣独立的评估师来制定系统评估计划，并进行控件评估。 控件评估每年进行一次，结果报告给相关方。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 已进行渗透测试并按“现状”进行漏洞扫描。 渗透测试结果可以重复，只需使用 nmap、pentest-tools.com 等常用工具即可。可以通过渗透测试结果了解攻击面（非结论性），确保没有可利用的漏洞。 另外，[Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)会提供漏洞信息和修正措施。|



### <a name="pci-dss-requirement-1131"></a>PCI DSS 要求 11.3.1

**11.3.1** 至少每年进行一次外部渗透测试。进行重大的基础结构或应用程序升级或修改（例如操作系统升级、向环境添加子网，或者向环境添加 Web 服务器）后，也应进行此类测试。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 11.3](#pci-dss-requirement-11-3) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 已进行渗透测试并按“现状”进行漏洞扫描。 渗透测试结果可以重复，只需使用 nmap、pentest-tools.com 等常用工具即可。可以通过渗透测试结果了解攻击面（非结论性），确保没有可利用的漏洞。 另外，[Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)会提供漏洞信息和修正措施。|



### <a name="pci-dss-requirement-1132"></a>PCI DSS 要求 11.3.2

**11.3.2** 至少每年进行一次内部渗透测试。进行重大的基础结构或应用程序升级或修改（例如操作系统升级、向环境添加子网，或者向环境添加 Web 服务器）后，也应进行此类测试。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 与独立评估师约定，对 Microsoft Azure 边界进行渗透测试。 还会定期进行红队练习，根据结果来改善安全。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 已进行渗透测试并按“现状”进行漏洞扫描。 渗透测试结果可以重复，只需使用 nmap、pentest-tools.com 等常用工具即可。可以通过渗透测试结果了解攻击面（非结论性），确保没有可利用的漏洞。 另外，[Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)会提供漏洞信息和修正措施。|



### <a name="pci-dss-requirement-1133"></a>PCI DSS 要求 11.3.3

**11.3.3** 在渗透测试过程中发现的可利用漏洞将会得到纠正，然后通过重复测试来验证纠正措施。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 将会根据确定的过程来监视 Microsoft Azure 平台组件，看其是否存在已知的安全漏洞。 <br /><br /><br /><br />每个季度都会对 Azure 生产环境的优先化组件有针对地进行综合性安全漏洞扫描，以便确定安全漏洞。 结果会报告给利益干系人，修正措施由团队在关闭案例时进行跟踪。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 已使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)来提供漏洞信息和修正措施，确保已修正 Contoso Webstore 演示 CDE 的所有未决问题。|



### <a name="pci-dss-requirement-1134"></a>PCI DSS 要求 11.3.4

**11.3.4** 如果使用分段将 CDE 与其他网络隔离，请至少每年执行一次渗透测试，以及在更改分段控件/方法后进行此类测试，验证分段方法是否行之有效，并将所有范围外系统与 CDE 中的系统隔离。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 将会根据确定的过程来监视 Microsoft Azure 平台组件，看其是否存在已知的安全漏洞。 <br /><br /><br /><br />每个季度都会对 Azure 生产环境的优先化组件有针对地进行综合性安全漏洞扫描，以便确定安全漏洞。 结果会报告给利益干系人，修正措施由团队在关闭案例时进行跟踪。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 已使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)来提供漏洞信息和修正措施，确保已修正 Contoso Webstore 演示 CDE 的所有未决问题。|



### <a name="pci-dss-requirement-11341"></a>PCI DSS 要求 11.3.4.1

**11.3.4.1** 仅适用于服务提供商的其他要求：如果使用分段，请确认 PCI DSS 范围，方法是：至少每隔六个月对分段控件执行一次渗透测试，以及在更改分段控件/方法后执行此类测试。

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 11.3.4](#pci-dss-requirement-11-3-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 已使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)来提供漏洞信息和修正措施，确保已修正 Contoso Webstore 演示 CDE 的所有未决问题。|



## <a name="pci-dss-requirement-114"></a>PCI DSS 要求 11.4

**11.4** 使用入侵检测和/或入侵预防技术来检测和/或防止对网络的入侵。 监视持卡人数据环境外围和持卡人数据环境中关键点的所有流量，提醒相关人员可能存在入侵。
使所有入侵检测和防护引擎、基线和签名保持最新。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 对其操作环境中的事件进行实时分析，IDS 系统针对可能损害系统的事件生成近实时警报。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 是一项 PaaS 服务，网络入侵检测和防护是 Azure 的责任。 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)提供入侵警报和漏洞修正措施。|



## <a name="pci-dss-requirement-115"></a>PCI DSS 要求 11.5

**11.5** 部署更改检测机制（例如，文件完整性监视工具），以便在出现针对关键系统文件、配置文件或内容文件的未授权修改（报告更改、添加和删除）时提醒相关人员，并将软件配置为至少一周执行一次关键文件比较操作。 

> [!NOTE]
> 进行更改检测时，关键文件通常是指那些不会定期更改的文件，而这些文件被修改则表明系统受到损害或存在受损风险。 更改检测机制（例如文件完整性监视产品）通常预配置了相关操作系统的关键文件。 其他关键文件，例如那些用于自定义应用程序的文件，必须由实体（即商家或服务提供商）来评估和定义。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 对于可能发生的更改以及可能影响服务安全性或可用性的事件，Microsoft Azure 会通过联机服务仪表板保留相关记录并通知客户。 如果 Microsoft Azure 客户的安全承诺和安全义务发生变化，将会及时在 Microsoft Azure 网站上进行更新。<br /><br />只允许授权的管理人员在 Microsoft Azure 生产环境中进行软件安装或更改，且相关操作必须按更改管理过程进行。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示版是一项 PaaS 服务，更改检测已使用 OMS 实施。 有关详细信息，请参阅 [PCI 指南 - 预安装的 OMS 解决方案](payment-processing-blueprint.md#oms-solutions)。<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS 要求 11.5.1

**11.5.1** 实施一个过程来响应更改检测解决方案生成的任何警报。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Azure 监视事件规则针对高风险操作和资产提供的监视级别已提高。 监视 Azure 托管的网络设备，看其是否符合既定的安全标准。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 更改警报通过 OMS 实施提供。 有关详细信息，请参阅 [PCI 指南 - 预安装的 OMS 解决方案](payment-processing-blueprint.md#oms-solutions)。<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS 要求 11.6

**11.6** 确保记录在进行安全监视和测试时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 更改警报通过 OMS 实施提供。 有关详细信息，请参阅 [PCI 指南 - 预安装的 OMS 解决方案](payment-processing-blueprint.md#oms-solutions)。<br /><br /><br /><br />|




