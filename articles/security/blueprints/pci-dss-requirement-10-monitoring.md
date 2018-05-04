---
title: Azure 付款处理蓝图 - 监视要求
description: PCI DSS 要求 10
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 708c57c1d7b79d3fd3c129de9a7ce4099ab6ac36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的监视要求 
## <a name="pci-dss-requirement-10"></a>PCI DSS 要求 10

**跟踪并监视对网络资源和持卡人数据的所有访问**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

日志记录机制和跟踪用户活动的功能很关键，可有效防止、检测或尽量降低数据泄漏的影响。 如果所有环境中都存在日志，则当出现故障时，就可以进行全面的跟踪、提醒和分析。 在没有系统活动日志的情况下，确定泄漏原因是很困难的，即使不是不可能。

## <a name="pci-dss-requirement-101"></a>PCI DSS 要求 10.1

**10.1** 实施审核日志措施，将对系统组件的所有访问关联到每个单独的用户。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 仅限具有相关职责的授权人员访问管理和诊断工具。 Microsoft Azure 根据“尽量减少特权”原则限制对生产环境中使用的工具进行的特许访问。 Microsoft Azure 记录和维护一个日志，其内容是在平台环境中对 Microsoft Azure 系统组件进行的所有个人用户访问。<br /><br />Microsoft Azure 平台组件（包括 OS、CloudNet、Fabric 等）已配置为记录和收集安全事件。 Microsoft Azure 平台中的管理员活动是受到记录的。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 广泛记录所有系统活动和用户活动（包括 CHD 日志记录）。 有关详细信息，请参阅 [PCI 指南 - 日志记录](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-102"></a>PCI DSS 要求 10.2

**10.2** 对所有系统组件实施自动化审核日志措施，以便重新构造以下事件：
- **10.2.1** 对持卡人数据进行的所有个人用户访问
- **10.2.2** 具有根目录权限或管理权限的任何个人执行的所有操作
- **10.2.3** 对所有审核日志的访问
- **10.2.4** 无效的逻辑访问尝试
- **10.2.5** 对标识和身份验证机制的使用和更改操作 — 包括但不限于创建新帐户和提升特权 — 以及对具有根权限或管理权限的帐户进行的所有更改、添加或删除操作
- **10.2.6** 初始化、停止或暂停审核日志
- **10.2.7** 创建和删除系统级别对象

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 仅限具有相关职责的授权人员访问管理和诊断工具。 Microsoft Azure 根据“尽量减少特权”原则限制对生产环境中使用的工具进行的特许访问。 Microsoft Azure 记录和维护一个日志，其内容是在平台环境中对 Microsoft Azure 系统组件进行的所有个人用户访问。<br /><br />Microsoft Azure 平台组件（包括 OS、CloudNet、Fabric 等）已配置为记录和收集安全事件。 Microsoft Azure 平台中的管理员活动是受到记录的。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 广泛记录所有系统活动和用户活动，其中包括 CHD 日志记录。 有关详细信息，请参阅 [PCI 指南 - 日志记录](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-103"></a>PCI DSS 要求 10.3

**10.3** 针对所有系统组件，至少记录每个事件的以下审核日志条目：
- **10.3.1** 用户识别
- **10.3.2** 事件类型
- **10.3.3** 日期和时间
- **10.3.4** 成功或失败指示
- **10.3.5** 事件起源
- **10.3.6** 受影响数据、系统组件或资源的标识或名称

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 已建立相关过程，将 Microsoft Azure 环境中的服务器和网络设备与 NTP 第 1 层时间服务器同步，而后者则同步到全球定位系统 (GPS) 卫星。 每隔五分钟自动执行一次同步。 Microsoft Azure 负责确保服务主机正确进行时间同步。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据 10.3 控制的要求，记录用户识别情况、事件类型、日期时间戳、成功/失败事件、事件源、资源名称。|



## <a name="pci-dss-requirement-104"></a>PCI DSS 要求 10.4

**10.4** 使用时间同步技术，同步所有关键系统时钟和时间，确保通过实施以下措施来获取、分发和存储时间。 
> [!NOTE]
> 例如，网络时间协议 (NTP) 是一种时间同步技术。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 已建立相关过程，将 Microsoft Azure 环境中的服务器和网络设备与 NTP 第 1 层时间服务器同步，而后者则同步到全球定位系统 (GPS) 卫星。 每隔五分钟自动执行一次同步。 Microsoft Azure 负责确保服务主机正确进行时间同步。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 针对 PaaS 服务的时间同步由 Azure 执行。|



### <a name="pci-dss-requirement-1041"></a>PCI DSS 要求 10.4.1

**10.4.1** 关键系统具有正确且一致的时间。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.4](#pci-dss-requirement-10-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 针对 PaaS 服务的时间同步由 Azure 执行。|



### <a name="pci-dss-requirement-1042"></a>PCI DSS 要求 10.4.2

**10.4.2** 时间数据受保护。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.4](#pci-dss-requirement-10-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 针对 PaaS 服务的时间同步由 Azure 执行。|



### <a name="pci-dss-requirement-1043"></a>PCI DSS 要求 10.4.3

**10.4.3** 时间设置从行业接受的事件源获得。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.4](#pci-dss-requirement-10-4) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 针对 PaaS 服务的时间同步由 Azure 执行。|



## <a name="pci-dss-requirement-105"></a>PCI DSS 要求 10.5

**10.5** 保护审核日志，使之不能被更改。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | FIM 和 IDS 工具在 Microsoft Azure 环境中实施。 Microsoft Azure 使用 EWS，支持对其操作环境中的事件进行实时分析。 对于可能会损害系统的事件，MA 和 AIMS 会生成近实时警报。 <br /><br />集中启用和保留对服务、用户和安全事件（Web 服务器日志、FTP 服务器日志等）的日志记录。 Azure 仅限授权人员根据职责访问审核日志。 事件日志在 Azure 安全存档基础结构上存档，保留 180 天。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



### <a name="pci-dss-requirement-1051"></a>PCI DSS 要求 10.5.1

**10.5.1** 仅限有工作需要的人员查看审核日志。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.5](#pci-dss-requirement-10-5) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



### <a name="pci-dss-requirement-1052"></a>PCI DSS 要求 10.5.2

**10.5.2** 防止未经授权修改审核日志文件。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.5](#pci-dss-requirement-10-5) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



### <a name="pci-dss-requirement-1053"></a>PCI DSS 要求 10.5.3

**10.5.3** 快速将审核日志文件备份到难以更改的中心日志服务器或介质。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.5](#pci-dss-requirement-10-5) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



### <a name="pci-dss-requirement-1054"></a>PCI DSS 要求 10.5.4

**10.5.4** 将外向技术的日志写入到安全的中心内部日志服务器或介质设备。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.5](#pci-dss-requirement-10-5) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



### <a name="pci-dss-requirement-1055"></a>PCI DSS 要求 10.5.5

**10.5.5** 对日志使用文件完整性监视或更改检测软件，确保现有日志数据不能在没有生成警报的情况下进行更改（不过，添加新数据不应引发警报）。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.5](#pci-dss-requirement-10-5) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以使用 Contoso Webstore 通过 Azure 对所有元素进行审核。 可以通过 [Azure 备份](https://azure.microsoft.com/services/backup/)备份到外部源。|



## <a name="pci-dss-requirement-106"></a>PCI DSS 要求 10.6

**10.6** 审核所有系统组件的日志和安全事件，标识异常的或可疑的活动。
 
> [!NOTE]
> 若要满足此要求，可以使用日志收集、分析和警报工具。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | FIM 和 IDS 工具在 Microsoft Azure 环境中实施。 Microsoft Azure 使用 EWS，支持对其操作环境中的事件进行实时分析。 对于可能会损害系统的事件，MA 和 AIMS 会生成近实时警报。 <br /><br />集中启用和保留对服务、用户和安全事件（Web 服务器日志、FTP 服务器日志等）的日志记录。 Azure 仅限授权人员根据职责访问审核日志。 事件日志在 Azure 安全存档基础结构上存档，保留 180 天。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)监视、报告和预防异常活动。 [Azure 顾问](/azure/advisor/advisor-security-recommendations)针对所有 Azure 资源提供一个一致且统一的建议视图。|



### <a name="pci-dss-requirement-1061"></a>PCI DSS 要求 10.6.1

**10.6.1** 至少每天审核一次以下内容：
- 所有安全事件
- 用于存储、处理或传输 CHD 和/或 SAD 的所有系统组件的日志
- 所有关键系统组件的日志
- 所有执行安全功能（例如，防火墙、入侵检测系统/入侵防护系统 (IDS/IPS)、身份验证服务器、电子商务重定向服务器，等等）的服务器和系统组件的日志。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.6](#pci-dss-requirement-10-6) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)监视、报告和预防异常活动。 [Azure 顾问](/azure/advisor/advisor-security-recommendations)针对所有 Azure 资源提供一个一致且统一的建议视图。|



### <a name="pci-dss-requirement-1062"></a>PCI DSS 要求 10.6.2

**10.6.2** 定期根据组织的政策和风险管理策略（取决于组织的年度风险评估）审核所有其他系统组件的日志。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.6](#pci-dss-requirement-10-6) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)监视、报告和预防异常活动。 [Azure 顾问](/azure/advisor/advisor-security-recommendations)针对所有 Azure 资源提供一个一致且统一的建议视图。|



### <a name="pci-dss-requirement-1063"></a>PCI DSS 要求 10.6.3

**10.6.3** 跟进在审核过程中发现的例外和异常。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 请参阅[要求 10.6](#pci-dss-requirement-10-6) 的“Microsoft Azure”部分。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)监视、报告和预防异常活动。 [Azure 顾问](/azure/advisor/advisor-security-recommendations)针对所有 Azure 资源提供一个一致且统一的建议视图。|



## <a name="pci-dss-requirement-107"></a>PCI DSS 要求 10.7

**10.7** 保留审核日志历史记录至少一年，至少可将三个月内的数据立即用于分析（例如，可以通过在线获取、存档或从备份还原的方式提供）。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 保留审核日志一年，最近 3 个月的数据可以通过内部门户立即进行访问。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 广泛记录所有系统活动和用户活动（包括 CHD 日志记录）。 有关详细信息，请参阅 [PCI 指南 - 日志记录](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-108"></a>PCI DSS 要求 10.8

**10.8** **仅适用于服务提供商的其他要求：**实施一个流程来及时检测和报告关键安全控制系统的故障，包括但不限于以下系统的故障：
- 防火墙
- IDS/IPS
- FIM
- 防病毒
- 物理访问控制
- 逻辑访问控制
- 审核日志记录机制
- 分段控制（如果使用） 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。



**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 使用 EWS，支持对其操作环境中的事件进行实时分析。 对于可能会损害系统的事件，MA 和 AIMS 会生成近实时警报。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 广泛记录所有系统活动和用户活动（包括 CHD 日志记录）。 有关详细信息，请参阅 [PCI 指南 - 日志记录](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-1081"></a>PCI DSS 要求 10.8.1

**10.8.1** **仅适用于服务提供商的其他要求：**及时响应任何关键安全控制的故障。 响应安全控制中的故障时，相关流程必须包括：
- 还原安全功能
- 确定并记录安全故障的时长（从开始到结束的日期和时间）
- 确定并记录故障原因，包括根本原因，并记录解决根本原因需要采取的修正措施
- 确定并解决在故障过程中引发的任何安全问题
- 执行风险评估，确定是否需要采取更多措施来解决安全故障
- 通过实施控制来防止故障原因再次出现 - 恢复对安全控件的监视 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 使用 EWS，支持对其操作环境中的事件进行实时分析。 对于可能会损害系统的事件，MA 和 AIMS 会生成近实时警报。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 广泛记录所有系统活动和用户活动（包括 CHD 日志记录）。 有关详细信息，请参阅 [PCI 指南 - 日志记录](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-109"></a>PCI DSS 要求 10.9

**10.9** 确保记录在监视对网络资源和持卡人数据进行的所有访问时使用过哪些安全策略和操作过程，并将其告知受影响的各方。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供了一个用例，说明了如何管理和保护 CHD。|




