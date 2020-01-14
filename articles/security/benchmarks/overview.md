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
ms.openlocfilehash: 2d12fa85fbc134ba2578795619db89f4a5058b26
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934749"
---
# <a name="overview-of-azure-security-controls"></a>Azure 安全控制概述

Azure 安全基准包含有助于提高 Azure 上应用程序和数据的安全性的建议。

此基准测试重点介绍以云为中心的控制领域。 这些控件与已知的安全基准（例如，Internet 安全（CIS）控件中心所述的版本7.1）一致。

Azure 安全基准中使用以下控件： 

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
- [渗透测试和 Red 团队练习](security-control-penetration-tests-red-team-exercises.md)

你还可以下载[Azure Security 基准 v1 excel 电子表格](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)。

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全基准建议 

每项建议都包含以下信息： 

- **AZURE ID**：对应于建议的 Azure 安全基准 ID。 
- **CI ID**：与此建议对应的 cis 基准建议。  
- **责任**：客户或服务提供商（或两者）是否负责实现此建议。 安全责任在公有云中共享。 某些安全控制仅适用于云服务提供商，因此提供程序负责解决这些控制。 这些是一般的观测值–对于一些单独的服务，责任将不同于 Azure 安全性基准中列出的内容。 每个服务的基线建议中介绍了这些差异。 
- **详细信息**：建议的基本原理，并链接到如何实现该建议。 如果 Azure 安全中心支持此建议，则还将列出这些信息。

我们欢迎你提供详细的反馈，并积极参与 Azure 安全基准测试。 如果要提供基准团队直接输入，请在[https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)填写表单。

## <a name="next-steps"></a>后续步骤

- 请参阅第一个安全控制：[网络安全](security-control-network-security.md)
- 下载[Azure Security 基准 v1 excel 电子表格](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
