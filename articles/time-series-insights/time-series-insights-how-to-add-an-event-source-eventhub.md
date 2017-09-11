---
title: "如何向 Azure 时序见解环境添加事件中心事件源 | Microsoft Docs"
description: "本教程介绍如何将连接到事件中心的事件源添加到时序见解环境"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="how-to-add-an-event-hub-event-source"></a>如何添加事件中心事件源

本教程介绍如何使用 Azure 门户将读取事件中心的事件源添加到时序见解环境。

## <a name="prerequisites"></a>先决条件

已创建了事件中心并将向其中写入事件。 有关事件中心的详细信息，请参阅 <https://azure.microsoft.com/services/event-hubs/>

> [使用者组] 每个时序见解事件源都需要具有其专用的使用者组，该组不与其他使用者共享。 如果多个读取器使用同一使用者组中的事件，则所有读取器都可能出现故障。 请注意还有一项限制，即每个事件中心最多只能有 20 个使用者组。 有关详细信息，请参阅[事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)。

## <a name="choose-an-import-option"></a>选择导入选项

可以手动输入事件源的设置，或从可用的事件中心中选择一个事件中心。
在“导入选项”选择器中，选择下列操作之一：

* 手动提供事件中心设置
* 从可用的订阅使用事件中心

### <a name="select-an-available-event-hub"></a>选择可用的事件中心

下表对“新建事件源”选项卡中的每个选项进行了说明，其中包含对将可用的事件中心选择为事件源的描述：

| 属性名称 | 说明 |
| --- | --- |
| 事件源名称 | 事件源的名称。 此名称在时序见解环境中必须是唯一的。
| 源 | 选择“事件中心”以创建事件中心事件源。
| 订阅 ID | 选择其中创建有此事件中心的订阅。
| 服务总线命名空间 | 选择包含事件中心的服务总线命名空间。
| 事件中心名称 | 选择事件中心的名称。
| 事件中心策略名称 | 选择共享访问策略，可在事件中心配置选项卡中创建此策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有“读取”权限。
| 事件中心使用者组 | 从事件中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。

### <a name="provide-event-hub-settings-manually"></a>手动提供事件中心设置

下表对“新建事件源”选项卡中的每个属性进行了说明，并对手动输入设置作了描述：

| 属性名称 | 说明 |
| --- | --- |
| 事件源名称 | 事件源的名称。 此名称在时序见解环境中必须是唯一的。
| 源 | 选择“事件中心”以创建事件中心事件源。
| 订阅 ID | 其中创建有此事件中心的订阅。
| 资源组 | 其中创建有此事件中心的订阅。
| 服务总线命名空间 | 服务总线命名空间是包含一组消息传递实体的容器。 创建新的事件中心后，还创建了 Service Bus 命名空间。
| 事件中心名称 | 事件中心的名称。 创建事件中心时，还为它提供了一个特定的名称
| 事件中心策略名称 | 可以在事件中心的“配置”选项卡上创建的共享访问策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有“读取”权限。
| 事件中心策略密钥 | 用于验证对服务总线命名空间的访问权限的共享访问密钥。 在此处键入主密钥或辅助密钥。
| 事件中心使用者组 | 从事件中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。

## <a name="next-steps"></a>后续步骤

1. 向环境添加数据访问策略[定义数据访问策略](time-series-insights-data-access.md)
1. 在[时序见解门户](https://insights.timeseries.azure.com)中访问环境
