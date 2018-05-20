---
title: Azure 付款处理蓝图 - 策略要求
description: PCI DSS 要求 12
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2fb238e9b95180d6156159c87ec008a71943e698
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的策略要求  
## <a name="pci-dss-requirement-12"></a>PCI DSS 要求 12

**维护用于解决所有人员的信息安全性问题的策略**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

强安全策略为整个实体设置安全基调，并告知相关人员需要做些什么。 所有人员都应该了解数据的敏感性，知道自己有责任进行保护。 就要求 12 来说，“人员”是指全职和兼职的员工、临时员工、承包商和顾问，是实体所在地区的“居民”，或者能够访问持卡人数据环境。

## <a name="pci-dss-requirement-121"></a>PCI DSS 要求 12.1

**12.1** 制定、发布、维护和分发安全策略。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护信息安全策略。|



### <a name="pci-dss-requirement-1211"></a>PCI DSS 要求 12.1.1

**12.1.1** 至少每年审核一次 安全策略，并在环境更改时更新该策略。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责至少每年更新一次其信息安全策略，或者在更改其持卡人数据环境 (CDE) 时进行更新。|



## <a name="pci-dss-requirement-122"></a>PCI DSS 要求 12.2

**12.2** 实施具有以下特点的风险评估流程：
- 至少每年执行一次，以及在对环境进行重大更改（例如，收购、合并、迁移等）时执行。
- 标识关键资产、威胁和漏洞。
- 生成正式的、有记录的风险分析。
- > 例如，风险评估方法包括但不限于：OCTAVE、ISO 27005、NIST SP 800-30。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责实施风险评估流程，解决要求 12.2 中列出的所有威胁。|



## <a name="pci-dss-requirement-123"></a>PCI DSS 要求 12.3

**12.3** 为关键技术制定使用策略，定义这些技术的正确使用方法。

> [!NOTE]
> 例如，关键技术包括但不限于：远程访问和无线技术、笔记本电脑、平板电脑、可移动电子介质、电子邮件使用和 Internet 使用。
确保这些使用策略需要符合以下条件。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1231"></a>PCI DSS 要求 12.3.1

**12.3.1** 由授权方公开批准

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1232"></a>PCI DSS 要求 12.3.2

**12.3.2** 使用此技术必须进行身份验证

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1233"></a>PCI DSS 要求 12.3.3

**12.3.3** 列出所有具有访问权限的此类设备和人员

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1234"></a>PCI DSS 要求 12.3.4

**12.3.4** 通过某种方法轻松准确地确定所有者、联系人信息和用途（例如，对设备使用标记、代码和/或清单）

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1235"></a>PCI DSS 要求 12.3.5

**12.3.5** 对技术的使用获得认可

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1236"></a>PCI DSS 要求 12.3.6

**12.3.6** 技术的网络位置获得认可

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确定对基于云的 VM、存储和支持服务来说，哪些网络位置可以接受。|



### <a name="pci-dss-requirement-1237"></a>PCI DSS 要求 12.3.7

**12.3.7** 列出公司批准的产品

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确定对基于云的 VM、存储和支持服务来说，哪些网络位置可以接受。|



### <a name="pci-dss-requirement-1238"></a>PCI DSS 要求 12.3.8

**12.3.8** 使用远程访问技术时，如果处于不活动的状态超过特定的时间，则自动断开会话

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 使用 Microsoft 公司的 AD 会话锁定功能，在一段时间的不活动后强制会话锁定。 不活动时间超过 30 分钟就会终止网络连接。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1239"></a>PCI DSS 要求 12.3.9

**12.3.9** 只允许供应商和业务合作伙伴根据需要为自己激活远程访问技术，且在使用完后必须立即停用

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-12310"></a>PCI DSS 要求 12.3.10

**12.3.10** 个人通过远程访问技术访问持卡人数据时，不得将持卡人数据复制、移动和存储到本地硬盘和可移动电子介质，除非因特定的业务需要已获得明确授权。
如果业务需求已获得授权，则在制定使用策略时，必须要求根据所有适用的 PCI DSS 要求对数据进行保护。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保当个人通过远程访问技术访问持卡人数据时，不得将持卡人数据复制、移动和存储到本地硬盘和可移动电子介质，除非因特定的业务需要已获得明确授权。|



## <a name="pci-dss-requirement-124"></a>PCI DSS 要求 12.4

**12.4** 确保安全策略和过程为所有人员明确定义了信息安全责任。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1241"></a>PCI DSS 要求 12.4.1

**12.4.1** 仅适用于服务提供商的其他要求：高级管理人员应明确责任，对持卡人数据进行保护并实施 PCI DSS 符合性计划，责任包括：
- 全面负责确保遵循 PCI DSS
- 针对 PCI DSS 符合性计划和高级管理人员通信制定一个章程 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 身为服务提供商的客户负责记录其 PCI 符合性计划。|



## <a name="pci-dss-requirement-125"></a>PCI DSS 要求 12.5

**12.5** 为个人或团队指定以下信息安全管理责任。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责为其员工定义和指定信息安全责任。|



### <a name="pci-dss-requirement-1251"></a>PCI DSS 要求 12.5.1

**12.5.1** 制定、记录和分发安全策略和过程。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责为其员工定义和指定信息安全责任。|



### <a name="pci-dss-requirement-1252"></a>PCI DSS 要求 12.5.2

**12.5.2** 监视和分析安全警报和信息，并将其分发到相应的人员。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责为其员工定义和指定信息安全责任。|



### <a name="pci-dss-requirement-1253"></a>PCI DSS 要求 12.5.3

**12.5.3** 制定、记录和分发安全事件响应和升级过程，确保及时且有效地处理所有情况。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1254"></a>PCI DSS 要求 12.5.4

**12.5.4** 管理用户帐户，包括进行添加、删除和修改操作。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



### <a name="pci-dss-requirement-1255"></a>PCI DSS 要求 12.5.5

**12.5.5** 监视和控制对数据的所有访问。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定和维护策略，要求对其 CDE 中的关键技术进行正确的使用、实施和身份验证。|



## <a name="pci-dss-requirement-126"></a>PCI DSS 要求 12.6

**12.6** 实施正式的安全意识计划，使所有人员都了解持卡人数据安全策略和过程的重要性。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责针对有权访问 CDE 的人员，围绕安全意识制定和维护相关策略。|



### <a name="pci-dss-requirement-1261"></a>PCI DSS 要求 12.6.1

**12.6.1** 对聘用人员进行培训，并且此类培训至少应每年一次。 

> [!NOTE]
> 培训方法可以因人员的角色及其对持卡人数据的访问权限级别而异。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保相关人员接受并确认信息安全和 PCI-DSS 意识的培训，至少每年一次。|



### <a name="pci-dss-requirement-1262"></a>PCI DSS 要求 12.6.2

**12.6.2** 要求相关人员至少每年确认一次其已阅读并理解安全策略和过程。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保相关人员接受并确认信息安全和 PCI-DSS 意识的培训，至少每年一次。|



## <a name="pci-dss-requirement-127"></a>PCI DSS 要求 12.7

**12.7** 对可能雇佣的人员进行雇佣前背景调查，尽量降低攻击者从内部资源进行攻击的风险。 （例如，背景调查包括既往雇佣经历、犯罪记录、信用记录、推荐人调查。） 

> [!NOTE]
> 对于某些职位的可能雇佣人员（例如商店收银员），由于这些人员在协助顾客进行交易时一次只接触一个卡号，因此此要求纯属建议。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保对能够访问 CDE 的人员进行全面的背景调查。|



## <a name="pci-dss-requirement-128"></a>PCI DSS 要求 12.8

**12.8** 维护和实施相关策略和过程，对与之共享持卡人数据的服务提供商或能够对持卡人数据的安全性造成影响的服务提供商进行管理，如下所示。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 对于与之共享持卡人数据的服务提供商或能够对 CDE 的安全性造成影响的服务提供商，客户负责监视其遵循 PCI 的情况。 客户必须保留一个列表，列出在其 CDE 中使用的所有服务提供商。|



### <a name="pci-dss-requirement-1281"></a>PCI DSS 要求 12.8.1

**12.8.1** 保留一个列表，列出服务提供商并对其所提供的服务进行说明。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 对于与之共享持卡人数据的服务提供商或能够对 CDE 的安全性造成影响的服务提供商，客户负责监视其遵循 PCI 的情况。 客户必须保留一个列表，列出在其 CDE 中使用的所有服务提供商。|



### <a name="pci-dss-requirement-1282"></a>PCI DSS 要求 12.8.2

**12.8.2** 保留一个书面协议，要求服务提供商确认对所拥有的或代表客户进行存储、处理或传输的持卡人数据的安全性负责，不得对客户的持卡人数据环境的安全性造成影响。 

> [!NOTE]
> 确认的具体措辞取决于双方达成的协议、所提供服务的详情，以及划分给各方的责任。 确认中的具体措辞不必是本要求中提供的。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责与服务提供商签署书面协议，确认维护持卡人数据安全的责任。|



### <a name="pci-dss-requirement-1283"></a>PCI DSS 要求 12.8.3

**12.8.3** 确保在相关人员的努力下，通过既定的流程促使服务提供商参与安全维护。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保在相关人员的努力下，通过既定的流程促使服务提供商参与安全维护。|



### <a name="pci-dss-requirement-1284"></a>PCI DSS 要求 12.8.4

**12.8.4** 保留一个计划，对服务提供商遵从 PCI DSS 的情况进行监视，至少每年一次。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责保留一个计划，对服务提供商遵从 PCI DSS 的情况进行监视，至少每年一次。|



### <a name="pci-dss-requirement-1285"></a>PCI DSS 要求 12.8.5

**12.8.5** 始终了解每个服务提供商管理的具体 PCI DSS 要求，以及实体所管理的相应要求。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责保留一份[责任摘要表](https://aka.ms/pciblueprintcrm32)，其中概述了客户负责的 PCI DSS 要求以及 Microsoft Azure 负责的相应要求。|



## <a name="pci-dss-requirement-129"></a>PCI DSS 要求 12.9

**12.9** 仅适用于服务提供商的其他要求：服务提供商以书面方式向客户确认对所拥有的或代表客户进行存储、处理或传输的持卡人数据的安全性负责，不得对客户的持卡人数据环境的安全性造成影响。 

> [!NOTE]
> 确认的具体措辞取决于双方达成的协议、所提供服务的详情，以及划分给各方的责任。 确认中的具体措辞不必是本要求中提供的。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 身为服务提供商的客户负责确认其有责任始终遵循 PCI。 |



## <a name="pci-dss-requirement-1210"></a>PCI DSS 要求 12.10

**12.10** 实施事件响应计划。 做好立即响应系统入侵的准备。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12101"></a>PCI DSS 要求 12.10.1

**12.10.1** 制定事件响应计划，在出现系统入侵事件时实施。 确保该计划至少解决以下问题：
- 在出现入侵事件时应负责的角色及其责任，以及应采取的通信和联系策略，至少应包括支付品牌通知
- 具体的事件响应过程
- 业务恢复和连续性过程
- 数据备份流程
- 分析有关报告入侵事件的法律要求
- 涵盖所有关键系统组件，明确这些组件的响应
- 引用或包括支付品牌的事件响应过程

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12102"></a>PCI DSS 要求 12.10.2

**12.10.2** 审核和测试计划（包括对要求 12.10.1 中列出的所有元素进行审核和测试），至少每年一次。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12103"></a>PCI DSS 要求 12.10.3

**12.10.3** 指定具体人员对警报进行全天候的响应。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12104"></a>PCI DSS 要求 12.10.4

**12.10.4** 向具有安全漏洞响应责任的人员提供适当的培训。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12105"></a>PCI DSS 要求 12.10.5

**12.10.5** 包括来自安全监视系统（包括但不限于：入侵检测、入侵预防、防火墙、文件完整性监视系统）的警报。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



### <a name="pci-dss-requirement-12106"></a>PCI DSS 要求 12.10.6

**12.10.6** 制定一个流程，根据获得的教训修改和改进事件响应计划，并汲取行业发展的技术。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责制定 IR 计划并进行测试，在测试过程中要考虑到任何与共享触摸点相关的客户控件，以及任何可利用 Azure 基础结构的客户应用程序。 客户有责任向 Azure 提供准确的联系人信息，因为在发生可能影响其应用程序或数据的事件时，需向其报告该事件。|



## <a name="pci-dss-requirement-1211"></a>PCI DSS 要求 12.11

**12.11** **仅适用于服务提供商的其他要求：** 至少按季进行审核，确认相关人员是否遵守安全策略和操作过程。
审核必须涵盖以下流程：
- 每日日志审核
- 防火墙规则集审核
- 对新系统应用配置标准
- 响应安全警报
- 变更管理流程 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 身为服务提供商的客户负责记录其对相关流程的审核情况，确认在 PCI 符合性方面的控制性能。|



### <a name="pci-dss-requirement-12111"></a>PCI DSS 要求 12.11.1

**12.11.1** 仅适用于服务提供商的其他要求：保留按季审核流程的记录，其中包括：
- 记录审核结果
- 由指定负责 PCI DSS 符合性计划的人员对相关结果进行审核和签收 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 身为服务提供商的客户负责记录其对相关流程的审核情况，确认在 PCI 符合性方面的控制性能。|




