---
title: "Azure 时序见解中的数据访问策略 | Microsoft Docs"
description: "本教程介绍如何在时序见解中管理数据访问策略"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 门户授予对时序见解环境的数据访问权限

Time Series Insights 环境有两个独立的访问策略类型：

* 管理访问策略
* 数据访问策略

两类策略都授予 Azure Active Directory 主体（用户和应用）对特定环境的各种权限。 主体（用户和应用）必须属于与包含环境的订阅关联的 Active Directory（或“Azure 租户”）。

管理访问策略授予的权限与环境配置相关，例如
*    创建和删除环境、事件源、引用数据集，以及
*    管理数据访问策略。

数据访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。

两类策略都可以清楚地区分环境管理访问权限和环境中数据的访问权限。 例如，可以对环境进行设置，从数据访问权限中删除环境的所有者/创建者。 此外，用户可以读取环境中的数据，但可能无法访问环境的配置。

## <a name="grant-data-access"></a>授予数据访问权限
以下步骤演示如何为用户主体授予数据访问权限：

1.    登录到 [Azure 门户](https://portal.azure.com)。
2.    在 Azure 门户左侧的菜单中，单击“所有资源”。
3.    选择时序见解环境。

  ![管理时序见解源 - 环境](media/data-access/getstarted-grant-data-access1.png)

4.    选择“数据平面访问”，单击“添加”

  ![管理时序见解源 - 添加](media/data-access/getstarted-grant-data-access2.png)

5.    单击“选择用户”。
6.    通过电子邮件搜索和选择用户。
7.    在“选择用户”边栏选项卡中单击“选择”。

  ![管理时序见解源 - 选择用户](media/data-access/getstarted-grant-data-access3.png)

8.    单击“选择角色”。
9.    若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询。
10.    在“选择角色”边栏选项卡中，单击“确定”。

  ![管理时序见解源 - 选择角色](media/data-access/getstarted-grant-data-access4.png)

11.    在“选择用户角色”边栏选项卡中，单击“确定”。
12.    你应会看到：

  ![管理时序见解源 - 结果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>后续步骤

* [创建事件源](time-series-insights-add-event-source.md)
* [发送事件](time-series-insights-send-events.md)到事件源
* 在[时序见解门户](https://insights.timeseries.azure.com)中查看环境

