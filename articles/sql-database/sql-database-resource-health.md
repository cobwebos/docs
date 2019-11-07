---
title: 使用 Azure 资源运行状况来监视 SQL 数据库运行状况
description: 使用 Azure 资源运行状况可以监视 SQL 数据库的运行状况，以及在 Azure 问题影响到 SQL 资源时帮助进行诊断和获取支持。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 10ce2da8ae8bcd8a12f0972d437a84a37a8390b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687503"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>使用资源运行状况排查 Azure SQL 数据库的连接问题

## <a name="overview"></a>概述

适用于 SQL 数据库的[资源运行状况](../service-health/resource-health-overview.md#get-started)可以帮助你在 Azure 问题影响到 SQL 资源时进行诊断和获取支持。 它通知你有关资源的当前和过去运行状况的信息，并帮助你缓解问题。 在需要有关 Azure 服务问题的帮助时，资源运行状况将提供技术支持。

![概述](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>运行状况检查

资源运行状况通过检查资源登录的成功与失败状态来确定 SQL 资源的运行状况。 目前，适用于 SQL 数据库资源的资源运行状况只会检查系统错误（而不是用户错误）导致的登录失败。 资源运行状况的状态每隔 1-2 分钟更新一次。

## <a name="health-states"></a>运行状态

### <a name="available"></a>可用

“可用”状态表示资源运行状况未检测到 SQL 资源中的系统错误导致的登录失败。

![可用](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>已降级

“已降级”状态表示资源运行状况检测到大多数登录成功，但也有一部分登录失败。 这些问题很有可能是暂时性登录错误。 若要减轻暂时性登录错误导致的连接问题所造成的影响，请在代码中实施[重试逻辑](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)。

![已降级](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>不可用

“不可用”状态表示资源运行状况检测到 SQL 资源登录一直失败。 如果资源长时间保持此状态，请与支持人员联系。

![不可用](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

运行状况为“未知”指示资源运行状况未收到此资源的相关信息已超过 10 分钟。 尽管此状态不是资源状态的最终指示，但它是故障排除过程中一个重要的数据点。 如果资源正在按预期方式运行，资源状态会在几分钟后更改为“可用”。 如果资源遇到问题，“未知”运行状态可能暗示平台中的事件正在影响资源。

![Unknown](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>历史信息

可在资源运行状况的“运行状况历史记录”部分访问最多 14 天的运行状况历史记录。 该部分还包含资源运行状况报告的停机问题的停机原因（如果有）。 目前，Azure 以两分钟粒度显示 SQL 数据库资源的停机时间。 实际停机时间可能小于一分钟 - 平均时间为 8 秒。

### <a name="downtime-reasons"></a>停机原因

如果 SQL 数据库遇到停机，将执行分析来确定原因。 在适当的情况下，资源运行状况的“运行状况历史记录”部分会报告停机原因。 停机原因通常在发生某个事件后的 30 分钟内发布。

#### <a name="planned-maintenance"></a>计划内维护

Azure 基础结构定期执行计划内维护 – 升级数据中心内的硬件或软件组件。 在数据库接受维护期间，SQL 可以终止某些现有连接并拒绝新连接。 在计划内维护期间出现的登录失败通常是暂时性的，[重试逻辑](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)可以帮助减轻影响。 如果持续遇到登录错误，请与支持人员联系。

#### <a name="reconfiguration"></a>重新配置

重新配置被视为暂时性状态，预期会不时地发生。 这些事件可能是负载均衡或软件/硬件故障触发的。 连接到云数据库的任何客户端生产应用程序应该实施可靠的连接[重试逻辑](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)，因为此逻辑有助于缓解这些情况，并且可让最终用户清晰地看到错误。

## <a name="next-steps"></a>后续步骤

- 详细了解[针对暂时性错误的重试逻辑](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [排查、诊断和预防 SQL 连接错误](./sql-database-connectivity-issues.md)
- 详细了解如何[配置资源运行状况警报](../service-health/resource-health-alert-arm-template-guide.md)
- 获取[资源运行状况](../service-health/resource-health-overview.md)的概述
- [资源运行状况常见问题解答](../service-health/resource-health-faq.md)
