---
title: Azure 安全基准简介
description: 安全基准简介
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3b492534bedde50d2413b88fa38cd6325ed5df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328252"
---
# <a name="azure-security-benchmark-introduction"></a>Azure 安全基准简介

每日在 Azure 中发布新的服务和功能，开发人员很快就会发布在这些服务上构建的新的云应用程序，攻击者始终会寻找新的方法来利用错误配置的资源。 云移动速度快，开发人员可快速移动，攻击者始终在移动。 你如何保持，并确保云部署是安全的？ 云系统的安全做法与本地系统有何不同？ 如何监视许多独立开发团队之间的一致性？

Microsoft 发现，使用 *安全基准* 有助于快速保护云部署。 你的云服务提供商提供的基准建议是在你的环境中选择特定安全配置设置的起点，并使你能够快速降低组织的风险。

Azure 安全基准包括一系列可用于帮助保护在 Azure 中使用的服务的高影响安全建议：

- **安全控制**：这些建议通常适用于 azure 租户和 azure 服务。 每个建议都标识了通常涉及到基准测试的规划、批准或实现的利益干系人列表。 
- **服务基线**：这些应用程序将这些控件应用于单个 Azure 服务，以提供有关该服务安全配置的建议。

## <a name="implement-the-azure-security-benchmark"></a>实现 Azure 安全性基准
- 通过查看企业控制和服务特定基线的[文档](overview.md)来**规划你的**Azure 安全性基准实现，以规划你的控制框架，并了解它如何映射到 ci (控制 v2.0) 和 NIST (SP800) framework。
- 使用 Azure 安全中心[规章相容性仪表板](../../security-center/security-center-compliance-dashboard.md)**监视**azure 安全基准状态 (和其他控制集) 的符合性。
- 通过 Azure 蓝图和 Azure 策略，**建立 guardrails**以自动执行安全配置，并强制符合 Azure 安全基准 (和) 组织中的其他要求。
 
请注意，Azure 安全基准 v2 与 Microsoft (以前的 Azure 安全罗盘) 安全 [最佳做法](/security/compass/microsoft-security-compass-introduction) 相结合，使 Azure 安全基准能够提供 microsoft azure 安全建议的单个综合视图。

## <a name="common-use-cases"></a>常见用例

Azure 安全基准通常用于解决以下客户或服务合作伙伴面临的常见难题：
- Azure 的新手，并正在寻找安全最佳做法，以确保安全部署。
- 改善现有 Azure 部署的安全状况，以优先处理顶级风险和缓解措施。
- 根据特定的安全准则，批准 Azure 服务以供技术和企业使用。
- 满足来自政府或高度管控行业（如金融和 (医疗保健）的客户的法规要求，以及需要为这些客户生成系统的服务供应商) 。 这些客户需要确保其 Azure 配置满足在行业框架中指定的安全功能，如 CIS、NIST 或 PCI。 Azure 安全基准提供一种高效的方法，其中的控件已预先映射到这些行业基准。

## <a name="terminology"></a>术语

术语 "控件"、"基准" 和 "基准" 通常在 Azure 安全基准文档中使用，并且必须了解 Azure 如何使用这些术语。


| 术语 | 说明 | 示例 |
|--|--|--|
| 控制 | “控制”是对需要实现的功能或活动的概要说明，并非特定于某种技术或实现。 | 数据保护是一项安全控制。 此控件包含必须解决的特定操作，以帮助确保数据受到保护。 |
| 基准 | “基准”包含针对特定技术（如 Azure）的安全建议。 这些建议按其所属的控制进行分类。 | Azure 安全基准包括特定于 Azure 平台的安全建议 |
| 基线 | 基线是对单个 Azure 服务的基准的实现。 每个组织决定在 Azure 实现范围内进行基准建议和相应的配置。 | Contoso 公司会按照 Azure SQL 安全基准中建议的配置来启用 Azure SQL 安全功能。

欢迎你提供有关 Azure 安全基准的反馈！ 建议你在以下反馈区域中提供评论。 如果你希望以更私密的方式将你的建议告知 Azure 安全基准检验团队，欢迎你在 https://aka.ms/AzSecBenchmark 中填写表单
