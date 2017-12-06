---
title: "Azure 付款处理蓝图 - 安全系统要求"
description: "PCI DSS 要求 6"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的安全系统要求 
## <a name="pci-dss-requirement-6"></a>PCI DSS 要求 6

**开发并维护安全系统和应用程序**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

不道德的攻击者利用安全漏洞获得对系统的特许访问权限。 许多此类漏洞由供应商提供的安全修补程序进行了修补，这些修补程序必须由管理系统的实体进行安装。 所有系统都必须安装所有适当的软件修补程序，防止恶意攻击者和恶意软件利用和损坏持卡人数据。

> [!NOTE]
> 适当的软件修补程序是指那些已经过足够的评估和测试，可以认为不会与现有的安全配置冲突的修补程序。 对于内部开发的应用程序来说，使用标准的系统开发流程和安全的编码技术即可避免各种漏洞。

## <a name="pci-dss-requirement-61"></a>PCI DSS 要求 6.1

**6.1** 建立一个流程来确定安全漏洞，使用可信的外部源来获取安全漏洞信息，并为新发现的安全漏洞指定风险等级（例如，“高”、“中”、“低”）。

> [!NOTE]
> 风险等级应基于行业最佳做法，并应考虑潜在的影响。 例如，评定漏洞的等级时，评定标准可以考虑 CVSS 基础分和/或供应商所作的分类，以及/或者受影响系统的类型。 
> 
> 评估漏洞并指定风险等级时，其方法因组织的环境和风险评估策略而异。 指定风险等级时，至少应确定所有据认为对环境来说属于“高风险”的漏洞。 除了考虑风险等级，还可以在漏洞对环境造成即时威胁、影响关键系统，以及/或者不处理就会造成潜在危害的情况下，将漏洞视为“严重”漏洞。 例如，关键系统可能包括安全系统、面向公众的设备和系统、数据库，以及其他用于存储、处理或传输持卡人数据的系统。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 制定和实施相关过程，扫描作用域范围内虚拟机监控程序主机上的漏洞。 使用适当的漏洞扫描工具，对服务器操作系统、数据库和网络设备进行漏洞扫描。 至少按季度进行漏洞扫描。 Microsoft Azure 与独立评估师约定，对 Microsoft Azure 边界进行渗透测试。 还会定期进行红队练习，根据结果来改善安全。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 可降低安全漏洞风险，方法是使用启用了 WAF 和 OWASP 规则集的应用程序网关。 有关详细信息，请参阅 [PCI 指南 - 降低安全漏洞风险](payment-processing-blueprint.md#application-gateway)。|



## <a name="pci-dss-requirement-62"></a>PCI DSS 要求 6.2

**6.2** 通过安装供应商提供的相应安全修补程序，确保所有系统组件和软件不受已知漏洞的危害。 安装关键安全修补程序的时间在发布后的一个月内。

> [!NOTE]
> 应按“要求 6.1”中定义的风险分级过程确定关键安全修补程序。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 负责确保所有网络设备和虚拟机监控程序 OS 软件均已安装供应商提供的相应安全修补程序，因此不受已知漏洞的危害。 除非客户请求不使用此服务，否则会实施现有的修补程序管理流程，确保防止操作系统级别的漏洞，即使存在此类漏洞，也会即时修正。 每月一次对生产服务器进行扫描，验证修补程序符合性。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 是一种 PaaS 服务解决方案。 Azure 对所有服务修补程序进行维护。|



## <a name="pci-dss-requirement-63"></a>PCI DSS 要求 6.3

**6.3** 安全地开发内部和外部软件应用程序（包括对应用程序进行基于 Web 的管理访问），如下所示：
- 遵循 PCI DSS（例如，进行安全的身份验证和日志记录）
- 基于行业标准和/或最佳做法
- 在软件开发生命周期内纳入信息安全措施 

> [!NOTE]
> 这适用于所有内部开发的软件，以及订购的软件或第三方开发的自定义软件。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 应用程序和终结点在开发时，遵循符合 DSS 要求的 Microsoft 安全开发生命周期 (SDL) 方法。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在设计时遵循行业最佳做法，目的是保护 CHD。 部署指南提供安全措施的详细信息，且日志记录已启用。|



### <a name="pci-dss-requirement-631"></a>PCI DSS 要求 6.3.1

**6.3.1** 在激活应用程序或将其发布给客户之前，删除开发、测试和/或自定义应用程序帐户、用户 ID 和密码。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 在进行生产部署之前，由 Azure 开发团队外部的指定安全顾问针对主版本进行最终安全审核 (FSR)，确保只发布已做好生产准备的应用程序。 必须确保已删除所有测试帐户和测试数据，这也是此最终审核的内容。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供一项进行记录和隔离的临时服务。 每个网络层都有专门的网络安全组 [NSG]。 有关详细信息，请参阅 [PCI 指南 - 网络安全组](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-632"></a>PCI DSS 要求 6.3.2

**6.3.2** 在发布到生产部门或客户之前，对自定义代码进行审核，以便确定所有潜在的编码漏洞（使用手动或自动过程），确保包括以下措施：
- 由原始代码作者之外的人员以及熟悉代码评审技巧和安全编码做法的人员对代码更改进行评审。
- 代码评审可确保代码是根据安全编码准则开发的。
- 在发布之前进行了适当的更正。
- 代码评审结果在产品发布之前由管理人员进行审核。 

> [!NOTE]
> 此代码评审要求适用于所有自定义代码（包括内部的和面向公众的），是系统开发生命周期的一部分。 
>
> 代码评审可以由资深内部人员或第三方来完成。 面向公众的 Web 应用程序还需要更多的控件，以应对实施后不断出现的威胁和漏洞，详见“PCI DSS 要求 6.6”中的定义。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 应用程序和终结点在开发时，遵循 Microsoft 安全开发生命周期 (SDL) 方法。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供一项进行记录和隔离的临时服务。 每个网络层都有专门的网络安全组 [NSG]。 有关详细信息，请参阅 [PCI 指南 - 网络安全组](payment-processing-blueprint.md#network-security-groups)。|



## <a name="pci-dss-requirement-64"></a>PCI DSS 要求 6.4

**6.4** 不管对系统组件进行何种更改，都应遵循更改控制流程和过程。 这些流程必须包括以下内容（参见要求 6.4.1 到 6.4.6）。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft 遵循有关软件开发安全注意事项的 NIST 指南，因为信息安全必须从系统初始就集成到 SDLC 中。 在 Microsoft SDL 中持续集成安全做法可以：<ul><li>早期发现安全漏洞和错误配置并缓解其影响</li><li>了解因进行必需的安全控制而带来的潜在软件编码挑战</li><li>确定共享的安全服务并重复使用安全最佳做法工具，通过经验证的方法和技术改善安全状况</li><li>强制实施 Microsoft 推出的已经很全面的风险管理计划</li></ul>Microsoft Azure 已建立更改和版本管理流程，用于控制主要更改的实施，其中包括：<ul><li>确定并记录计划的更改</li><li>在产品规划期间确定业务目标、优先级和方案</li><li>指定功能/组件设计</li><li>根据预定义的标准/核对清单进行操作就绪性审核，评估总体风险/影响</li><li>根据适用于 DEV（开发）、INT（集成测试）、STAGE（预生产）和 PROD（生产）环境的准入/退出标准，视情况进行测试、授权和更改管理。 客户对自己托管在 Microsoft Azure 中的应用程序负责。</li></ul> |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示版提供一项进行记录和隔离的临时服务。 <br /><br />每个网络层都有专门的网络安全组 [NSG]。 有关详细信息，请参阅 [PCI 指南 - 网络安全组](payment-processing-blueprint.md#network-security-groups)。<br /><br />使用 Operations Management Suite 来记录更改，使用 Runbook 来收集日志。 [Operations Management Suite (OMS)](/azure/operations-management-suite/) 可以大量记录更改。 可以对更改进行审核和验证，确保准确性。 如需更具体的指南，请参阅 [PCI 指南 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-641"></a>PCI DSS 要求 6.4.1

**6.4.1** 将开发/测试环境与生产环境分开，通过访问控制强制分开。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供一项进行记录和隔离的临时服务。 每个网络层都有专门的网络安全组 [NSG]。 有关详细信息，请参阅 [PCI 指南 - 网络安全组](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-642"></a>PCI DSS 要求 6.4.2

**6.4.2** 将开发/测试环境和生产环境的职责分开。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供一项进行记录和隔离的临时服务。 每个网络层都有专门的网络安全组 [NSG]。 有关详细信息，请参阅 [PCI 指南 - 网络安全组](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-643"></a>PCI DSS 要求 6.4.3

**6.4.3** 生产数据（实时 PAN）不用于测试或开发。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 没有实时主帐号 (PAN) 数据。|



### <a name="pci-dss-requirement-644"></a>PCI DSS 要求 6.4.4

**6.4.4** 在系统激活或进入生产之前，从系统组件中删除测试数据和帐户。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 没有任何测试帐户。|



### <a name="pci-dss-requirement-645"></a>PCI DSS 要求 6.4.5

**6.4.5** 用于实施安全修补程序和软件修改的更改控制过程必须包括以下内容：
- **6.5.4.1** 记录影响。
- **6.5.4.2** 记录由授权方进行的更改审批。
- **6.5.4.3** 进行功能测试，验证所做的更改是否不会对系统安全性造成负面影响。
- **6.5.4.4** 退出过程。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 是一种 PaaS 服务解决方案。 Azure 对所有服务修补程序进行维护。|



### <a name="pci-dss-requirement-646"></a>PCI DSS 要求 6.4.6

**6.4.6** 在完成重大更改以后，必须在所有新的或更改的系统和网络上实施所有相关的 PCI DSS 要求，并对文档记录进行相应的更新。

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 是一种 PaaS 服务解决方案。 Azure 对所有服务修补程序进行维护。|



## <a name="pci-dss-requirement-65"></a>PCI DSS 要求 6.5

**6.5** 解决软件开发流程中的常见编码漏洞，如下所示：
- 至少每年对开发人员培训一次最新的安全编码技术，包括如何避免常见的编码漏洞。
- 根据安全编码准则开发应用程序。

> [!NOTE]
> 此版 PCI DSS 发布时，针对 6.5.1 到 6.5.10 中所列漏洞的行业最佳做法是最新的。 但是，随着漏洞管理的行业最佳做法（例如，“OWASP 指南”、“SANS CWE 前 25 个漏洞”、“CERT 安全编码”，等等）的更新，也必须针对这些要求使用最新的最佳做法。 
> 
> [!NOTE]
> 下面的要求 6.5.1 到 6.5.6 适用于所有应用程序（内部或外部）。 下面的要求 6.5.7 到 6.5.10 适用于 Web 应用程序和应用程序接口（内部或外部）。 

- **6.5.1** 注入漏洞，尤指 SQL 注入。 另外还需注意 OS 命令注入、LDAP 和 XPath 注入漏洞以及其他注入漏洞。
- **6.5.2** 缓冲区溢出
- **6.5.3** 不安全的加密存储
- **6.5.4** 不安全的通信
- **6.5.5** 不适当的错误处理
- **6.5.6** 在漏洞确定过程（详见“PCI DSS 要求 6.1”）中确定的所有“高风险”漏洞
- **6.5.7** 跨站点脚本 (XSS)
- **6.5.8** 访问控制不适当（例如以不安全的方式直接引用对象、无法限制 URL 访问权限、目录遍历、无法限制用户访问函数）
- **6.5.9** 跨网站请求伪造 (CSRF)
- **6.5.10** 身份验证和会话管理中断

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 请参阅[要求 6.4](#pci-dss-requirement-6-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示版提供安全开发指南，这是一个 DFD 和威胁模型，用于演示安全开发做法。|



## <a name="pci-dss-requirement-66"></a>PCI DSS 要求 6.6

**6.6** 对于面向公众的 Web 应用程序，需通过以下方法之一不断解决新威胁和漏洞，确保这些应用程序不受已知攻击的威胁：

- 通过手动或自动应用程序漏洞安全评估工具或方法审核面向公众的 Web 应用程序，至少每年实施一次，以及在进行更改后实施 

   > [!NOTE]
   > 此评估不同于针对要求 11.2 执行的漏洞扫描。 

- 在面向公众的 Web 应用程序前面安装用于检测和防止基于 Web 的攻击的自动化技术解决方案（例如，Web 应用程序防火墙），对所有流量持续进行检查。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 在将应用程序部署到生产环境之前，Microsoft Azure 会在其 SDL 流程中对面向公众的 Web 应用程序进行测试。 另外，Microsoft 还会定期扫描生产中所有面向公众的 Web 应用程序，以便检测任何可能的漏洞。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 应用解决方案可降低安全漏洞风险，方法是使用启用了 WAF 和 OWASP 规则集的应用程序网关。 有关详细信息，请参阅 [PCI 指南 - 降低安全漏洞风险](payment-processing-blueprint.md#application-gateway)。|



## <a name="pci-dss-requirement-67"></a>PCI DSS 要求 6.7

**6.7** 确保记录在开发和维护安全系统和应用程序时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 应用解决方案可降低安全漏洞风险，方法是使用启用了 WAF 和 OWASP 规则集的应用程序网关。 有关详细信息，请参阅 [PCI 指南 - 降低安全漏洞风险](payment-processing-blueprint.md#application-gateway)。|




