---
title: "如何向 Azure 时序见解环境添加 IoT 中心事件源 | Microsoft Docs"
description: "本教程介绍如何将已连接至 IoT 中心的事件源添加到时序见解环境"
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
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="how-to-add-an-iot-hub-event-source"></a>如何添加 IoT 中心事件源

本教程介绍如何使用 Azure 门户将从 IoT 中心读取的事件源添加到时序见解环境。

## <a name="prerequisites"></a>先决条件

已创建 IoT 中心，并要向其写入事件。 有关 IoT 中心的详细信息，请参阅 <https://azure.microsoft.com/services/iot-hub/>

> [使用者组] 每个时序见解事件源都需要具有其专用的使用者组，该组不与其他使用者共享。 如果多个读取器使用同一使用者组中的事件，则所有读取器都可能出现故障。 有关详细信息，请参阅 [IoT 中心开发人员指南](../iot-hub/iot-hub-devguide.md)。

## <a name="choose-an-import-option"></a>选择导入选项

可手动输入事件源的设置，或者从 IoT 中心选择可用的 IoT 中心。
在“导入选项”选择器中，选择下列操作之一：

* 手动提供 IoT 中心设置
* 从可用的订阅使用 IoT 中心

### <a name="select-an-available-iot-hub"></a>选择可用的 IoT 中心

下表对“新建事件源”选项卡中的每个选项进行了说明，其中包含对将可用的 IoT 中心选择为事件源的描述：

| 属性名称 | 说明 |
| --- | --- |
| 事件源名称 | 事件源的名称。 此名称在时序见解环境中必须是唯一的。
| 源 | 选择“IoT 中心”以创建 IoT 中心事件源。
| 订阅 ID | 选择其中创建有此 IoT 中心的订阅。
| IoT 中心名称 | 选择 IoT 中心的名称。
| IoT 中心策略名称 | 选择共享访问策略，可在 IoT 中心设置选项卡中找到此策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有服务连接权限。
| IoT 中心使用者组 | 从 IoT 中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。

### <a name="provide-iot-hub-settings-manually"></a>手动提供 IoT 中心设置

下表对“新建事件源”选项卡中的每个属性进行了说明，并对手动输入设置作了描述：

| 属性名称 | 说明 |
| --- | --- |
| 事件源名称 | 事件源的名称。 此名称在时序见解环境中必须是唯一的。
| 源 | 选择“IoT 中心”以创建 IoT 中心事件源。
| 订阅 ID | 在其中创建此 IoT 中心的订阅。
| 资源组 | 在其中创建此 IoT 中心的订阅。
| IoT 中心名称 | IoT 中心的名称。 创建 IoT 中心时，也为它提供了一个特定的名称
| IoT 中心策略名称 | 共享访问策略，可在 IoT 中心设置选项卡中创建此策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有服务连接权限。
| IoT 中心策略密钥 | 用于验证对服务总线命名空间的访问权限的共享访问密钥。 在此处键入主密钥或辅助密钥。
| IoT 中心使用者组 | 从 IoT 中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。

## <a name="next-steps"></a>后续步骤

1. 向环境添加数据访问策略[定义数据访问策略](time-series-insights-data-access.md)
1. 在[时序见解门户](https://insights.timeseries.azure.com)中访问环境
