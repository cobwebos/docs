---
title: 使用 Azure 资源运行状况监视数据库运行状况
description: 使用 Azure 资源运行状况监视 Azure SQL 数据库和 Azure SQL 托管实例运行状况，有助于在 Azure 问题影响 SQL 资源时进行诊断和获取支持。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 27865afd356be9eac64083c1ebdeb6ced43dbd18
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986936"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>使用资源运行状况排查 Azure SQL 数据库和 Azure SQL 的连接问题托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

适用于 Azure SQL 数据库和 Azure SQL 托管实例的[资源运行状况](../../service-health/resource-health-overview.md#get-started)有助于在 Azure 问题影响 SQL 资源时进行诊断和获取支持。 它通知你有关资源的当前和过去运行状况的信息，并帮助你缓解问题。 当你需要有关 Azure 服务问题的帮助时，资源运行状况提供技术支持。

![概述](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>运行状况检查

资源运行状况通过检查资源登录的成功与失败状态来确定 SQL 资源的运行状况。 目前，SQL 数据库资源资源运行状况仅检查因系统错误而导致的登录失败，而不检查用户错误。 每隔1到2分钟更新资源运行状况状态。

## <a name="health-states"></a>运行状况状态

### <a name="available"></a>可用

"**可用**" 状态表示资源运行状况未检测到 SQL 资源上的系统错误导致登录失败。

![可用](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>已降级

“已降级”状态表示资源运行状况检测到大多数登录成功，但也有一部分登录失败。 这些问题很有可能是暂时性登录错误。 为了降低暂时性登录错误引起的连接问题的影响，请在代码中实现[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。

![已降级](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>不可用

“不可用”状态表示资源运行状况检测到 SQL 资源登录一直失败。 如果资源长时间保持此状态，请联系支持人员。

![不可用](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>未知

运行状况为“未知”指示资源运行状况未收到此资源的相关信息已超过 10 分钟。 尽管此状态不是资源状态的最终指示，但它是故障排除过程中一个重要的数据点。 如果资源正在按预期方式运行，资源状态会在几分钟后更改为“可用”。 如果资源遇到问题，“未知”运行状态可能暗示平台中的事件正在影响资源。

![未知](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>历史信息

在资源运行状况的 "运行状况历史记录" 部分中，最多可以访问14天的运行状况历史记录。 该部分还包含资源运行状况报告的停机问题的停机原因（如果有）。 目前，Azure 以两分钟的粒度显示数据库资源的停机时间。 实际停机时间可能小于一分钟。 平均为8秒。

### <a name="downtime-reasons"></a>停机原因

当数据库遇到停机时间时，将执行分析来确定原因。 在适当的情况下，资源运行状况的“运行状况历史记录”部分会报告停机原因。 停机原因通常在发生某个事件后的 30 分钟内发布。

#### <a name="planned-maintenance"></a>计划内维护

Azure 基础结构定期执行计划内维护–数据中心内硬件或软件组件的升级。 在数据库进行维护时，Azure SQL 可能会终止一些现有连接并拒绝新连接。 在计划内维护期间遇到的登录失败通常是暂时性的，[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)有助于降低影响。 如果继续遇到登录错误，请联系支持人员。

#### <a name="reconfiguration"></a>重新配置

重新尝试将被视为暂时性的情况，并预计需要一段时间。 这些事件可能是负载均衡或软件/硬件故障触发的。 连接到云数据库的任何客户端生产应用程序应该实施可靠的连接[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)，因为此逻辑有助于缓解这些情况，并且可让最终用户清晰地看到错误。

## <a name="next-steps"></a>后续步骤

- 详细了解[暂时性错误的重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
- [排查、诊断和防止 SQL 连接错误](troubleshoot-common-connectivity-issues.md)。
- 详细了解如何[配置资源运行状况警报](../../service-health/resource-health-alert-arm-template-guide.md)。
- 大致了解[资源运行状况](../../application-gateway/resource-health-overview.md)。
- 查看[资源运行状况常见问题解答](../../service-health/resource-health-faq.md)。
