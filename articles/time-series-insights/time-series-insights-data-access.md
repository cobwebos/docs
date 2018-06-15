---
title: 配置安全性以访问和管理 Azure 时序见解 | Microsoft Docs
description: 本文介绍如何将安全性和权限配置为管理访问策略和数据访问策略以保护 Azure 时序见解。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 4306d22f03faa55fb6fc8be1a359aea3410e8038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653804"
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

## <a name="grant-data-access"></a>授予数据访问权限
遵循以下步骤向用户主体授予数据访问权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找时序见解环境。 在“搜索”框中，键入“时序见解”。 在搜索结果中选择“时序环境”。 

3. 从列表中选择时序见解环境。
   
4. 选择“数据访问策略”，然后选择“+ 添加”。
  ![管理时序见解源 - 环境](media/data-access/getstarted-grant-data-access1.png)

5. 选择“选择用户”。  搜索用户名称或电子邮件地址，查找要添加的用户。 单击“选择”确认选择。 

   ![管理时序见解源 - 添加](media/data-access/getstarted-grant-data-access2.png)

6. 选择“选择角色”。 为用户选择相应的访问角色：
   - 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。 
   - 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询。

   选择“确定”确认角色选择。

   ![管理时序见解源 - 选择用户](media/data-access/getstarted-grant-data-access3.png)

8. 在“选择用户角色”页中，选择“确定”。

   ![管理时序见解源 - 选择角色](media/data-access/getstarted-grant-data-access4.png)

9. “数据访问策略”页列出了用户和每个用户的角色。

   ![管理时序见解源 - 结果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>后续步骤
* 了解[如何向 Azure 时序见解环境添加事件中心事件源](time-series-insights-how-to-add-an-event-source-eventhub.md)。
* [发送事件](time-series-insights-send-events.md)到事件源。
* 在[时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
