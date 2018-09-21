---
title: 配置安全性以访问和管理 Azure 时序见解 | Microsoft Docs
description: 本文介绍如何将安全性和权限配置为管理访问策略和数据访问策略以保护 Azure 时序见解。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364801"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 门户授予对时序见解环境的数据访问权限

Time Series Insights 环境有两个独立的访问策略类型：

* 管理访问策略
* 数据访问策略

两类策略都授予 Azure Active Directory 主体（用户和应用）对特定环境的各种权限。 主体（用户和应用）必须属于与包含环境的订阅关联的 Active Directory（称为 Azure 租户）。

管理访问策略授予的权限与环境配置相关，例如
*   创建和删除环境、事件源、引用数据集，以及
*   管理数据访问策略。

数据访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。

两类策略都可以清楚地区分环境管理访问权限和环境中数据的访问权限。 例如，可以对环境进行设置，从数据访问权限中删除环境的所有者/创建者。 此外，用户和服务可以读取环境中的数据，但可能无权访问环境的配置。

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>后续步骤

* 了解[如何向 Azure 时序见解环境添加事件中心事件源](time-series-insights-how-to-add-an-event-source-eventhub.md)。
* [发送事件](time-series-insights-send-events.md)到事件源。
* 在[时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
