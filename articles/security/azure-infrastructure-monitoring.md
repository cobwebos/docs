---
title: 监视 Azure 基础结构
description: 本文讨论如何监视 Azure 生产网络。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101402"
---
# <a name="monitoring-of-azure-infrastructure"></a>监视 Azure 基础结构   

## <a name="configuration-and-change-management"></a>配置和更改管理
Microsoft Azure 每年都会检查和更新硬件、软件和网络设备的配置设置和基线配置。 在从开发和/或测试环境进入生产环境之前，需开发、测试和批准更改。

Azure 安全性和符合性团队以及服务团队会对基于 Azure 的服务所需的基线配置进行评审。 服务团队的评审是部署其产品服务前进行的测试的一部分。

## <a name="vulnerability-management"></a>漏洞管理
安全更新管理可帮助保护系统免受已知漏洞的侵害。 Azure 使用集成的部署系统来管理 Microsoft 软件的安全更新的分发和安装。 Azure 还可以利用 Microsoft 安全响应中心 (MSRC) 的资源。 MSRC 一年中每天每时识别、监视、响应和解决安全事件以及云漏洞。

## <a name="vulnerability-scanning"></a>漏洞扫描
对服务器操作系统、数据库和网络设备进行漏洞扫描。 至少按季度进行漏洞扫描。 Microsoft Azure 与独立评估师约定，对 Microsoft Azure 边界进行渗透测试。 还会定期进行红队练习，根据结果来改善安全。

## <a name="protective-monitoring"></a>保护监视
Microsoft Azure 安全定义了主动监视的要求。 服务团队配置符合这些要求的主动监视工具。 主动监视工具包括 Monitoring Agent (MA) 和 System Center Operations Manager。 这些工具配置为在需要立即采取措施的情况下向 Microsoft Azure 安全管理人员提供实时警报。

## <a name="incident-management"></a>事件管理
Microsoft 实施安全事件管理过程，以在发生事件时加速对事件的协调响应。

如果 Microsoft 发现对存储在其设备或设施上的客户数据未经授权的访问，或者未经授权访问这些设备或设施导致客户数据丢失、泄露或更改，Microsoft 将采取以下措施：

- 立即通知客户相关安全事件
- 立即调查安全事件，并向客户提供有关安全事件的详细信息
- 执行合理的提示性步骤，以减轻影响并将安全事件导致的损害降至最低。

已建立一个事件管理框架，其中定义了角色并分配了职责。 Windows Azure 安全事件管理 (WASIM) 团队负责管理安全事件，包括升级，并确保在必要时专家团队的参与。 Azure 操作管理员负责监督安全和隐私事件的调查及解决。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产操作和管理](azure-infrastructure-operations.md)
- [Azure 基础结构的完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)
