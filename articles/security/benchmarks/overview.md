---
title: Azure 安全基准概述
description: 安全基准概述
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587526"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Azure 安全基准概述

Azure 安全基准包含可帮助您提高 Azure 上应用程序和数据安全性的建议。

此基准测试侧重于以云为中心的控制区域。 这些控件与众所周知的安全基准一致，例如互联网安全中心 （CIS） 控制版本 7.1 中描述的基准。

Azure 安全基准测试中使用了以下控件： 

- [网络安全](security-control-network-security.md)
- [日志记录和监视](security-control-logging-monitoring.md)
- [标识和访问控制](security-control-identity-access-control.md)
- [数据保护](security-control-data-protection.md)
- [漏洞管理](security-control-vulnerability-management.md)
- [清单和资产管理](security-control-inventory-asset-management.md)
- [安全配置](security-control-secure-configuration.md)
- [恶意软件防护](security-control-malware-defense.md)
- [数据恢复](security-control-data-recovery.md)
- [事件响应](security-control-incident-response.md)
- [渗透测试和红队练习](security-control-penetration-tests-red-team-exercises.md)

您还可以下载 Azure[安全基准 v1 excel 电子表格](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)。

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全基准建议 

每个建议包括以下信息： 

- **Azure ID**：对应于建议的 Azure 安全基准 ID。 
- **独联体 ID：** 符合本建议的独联体基准建议。  
- **责任**：无论是客户还是服务提供商（或两者）都负责实施此建议。 安全责任在公共云中共享。 某些安全控制仅对云服务提供商可用，因此提供商负责解决这些问题。 这些是一般性观察 - 对于某些单个服务，其责任将不同于 Azure 安全基准测试中列出的责任。 这些差异在个别服务的基线建议中描述。 
- **详情**：建议的理由及如何实施该建议的指导。 如果 Azure 安全中心支持该建议，该信息也将列出。

我们欢迎您提供详细反馈并积极参与 Azure 安全基准工作。 如果您想提供基准团队直接输入，请在 上[https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)填写表格。

## <a name="next-steps"></a>后续步骤

- 请参阅第一个安全控制：[网络安全](security-control-network-security.md)
- 阅读[Azure 安全基准简介](introduction.md)
- 下载[Azure 安全基准 v1 Excel 电子表格](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
