---
title: "向 Azure 时序见解环境添加事件源 | Microsoft Docs"
description: "在本教程中，你将事件源连接到时序见解环境"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/26/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 门户为时序见解环境创建事件源

时序见解事件源派生自事件代理（例如 Azure 事件中心）。 时序见解直接连接到事件源，不需用户编写一行代码即可引入数据流。 时序见解目前支持 Azure 事件中心和 Azure IoT 中心，以后会添加更多的事件源。

## <a name="steps-to-add-an-event-source-to-your-environment"></a>将事件源添加到环境的步骤

1.    登录到 [Azure 门户](https://portal.azure.com)。
2.    在 Azure 门户左侧的菜单中，单击“所有资源”。
3.    选择时序见解环境。

  ![创建时序见解事件源](media/add-event-source/getstarted-create-eventsource1.png)

4.    选择“事件源”，单击“+ 添加”。

  ![创建时序见解事件源 - 详细信息](media/add-event-source/getstarted-create-eventsource2.png)

5.    指定事件源的名称。 该名称与来自此事件源的所有事件相关联，在查询时可用。
6.    从当前订阅的事件中心资源列表中选择事件中心，或者选择导入选项“手动提供事件中心设置”以指定位于其他订阅中的事件中心。 必须以 JSON 格式发布事件。
7.    选择在事件中心具有读取权限的策略。
8.    指定事件中心使用者组。

  > [!IMPORTANT]
  > 请确保该使用者组没有被任何其他服务（例如流分析作业或其他时序见解环境）使用。 如果使用者组由其他服务使用，则此环境和其他服务的读取操作会受到负面影响。 如果使用“$Default”作为使用者组，则可能会被其他读取器重复使用。

9.    单击“创建”。

创建事件源以后，时序见解就会自动将数据流式传输到你的环境中。

## <a name="next-steps"></a>后续步骤

* [发送事件](time-series-insights-send-events.md)到事件源
* 在[时序见解门户](https://insights.timeseries.azure.com)中查看环境

