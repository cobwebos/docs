---
title: Azure 时序见解预览版管理 - 如何预配和管理 Azure 时序见解预览版。 | Microsoft Docs
description: 了解如何预配和管理 Azure 时序见解预览版。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273491"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>如何预配和管理 Azure 时序见解预览版

本文档介绍如何使用 [Azure 门户](https://portal.azure.com/)创建和管理 Azure 时序见解预览版环境。

## <a name="overview"></a>概述

下面简单介绍如何预配 Azure 时序见解预览版环境：

* Azure 时序见解预览版环境是 **PAYG** 环境。
  * 在创建过程中，需提供时序 ID。 时序 ID 最多可以是三个键。 若要详细了解如何选择时序 ID，请阅读[如何选择时序 ID](./time-series-insights-update-how-to-id.md)。
  * 预配 Azure 时序见解预览版环境时，，请创建两个 Azure 资源：

    * Azure 时序见解预览版环境  
    * Azure 存储常规用途 V1 帐户
  
    了解[如何规划环境](./time-series-insights-update-plan.md)。

    >[!IMPORTANT]
    > 对于使用 V2 帐户的客户，请勿在要使用的存储帐户上启用冷/存档属性。

* 每个 Azure 时序见解预览版环境都可以与一个事件源相关联（可选）。 有关详细信息，请阅读[如何添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)和[如何添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
  * 需在此步骤提供一个 Timestamp ID 属性和唯一的使用者组。 这样做可确保环境能够访问相应的事件。

* 预配完成后，即可根据业务要求修改访问策略和其他环境属性。

## <a name="new-environment-creation"></a>新建环境

以下步骤介绍如何创建 Azure 时序见解预览版环境：

1. 在“SKU”菜单下选择“PAYG”按钮。 提供一个环境名称，指定要使用的订阅组和资源组。 然后，选择一个支持的位置，以便在其中托管环境。

1. 输入时序 ID

    >[!NOTE]
    > * 时序 ID 区分大小写且不可更改（在设置后不能更改）。
    > * 时序 ID 最多可以是 3 个键。
    > * 若要详细了解如何选择时序 ID，请阅读[如何选择时序 ID](./time-series-insights-update-how-to-id.md)。

1. 创建一个 Azure 存储帐户，方法是选择 Azure 存储帐户名称并指定复制选项。 这样做会自动创建 Azure 存储常规用途 V1 帐户。 将会在以前选择的 Azure 时序见解预览版环境所在区域中创建该帐户。

    ![创建 Azure 时序见解实例。][1]

1. 可以选择添加事件源。

   * 时序见解支持使用 [Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和[事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作为选项。 虽然在创建环境时只能添加单个事件源，但可以在以后添加其他的事件源。 最好是创建唯一的使用者组，确保所有事件对 Azure 时序见解预览版实例可见。 在添加事件源时，可以选择现有的使用者组，也可以创建新的使用者组。

   * 还应指定相应的 Timestamp 属性。 默认情况下，Azure Time 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 确保验证在这种情况下你是决定使用还是不使用 Timestamp 属性。

    ![将事件源添加到实例。][2]

1. 查看并创建

    ![将事件源添加到实例。][3]

确认环境是否已使用所需设置进行预配。

## <a name="management"></a>管理

可以使用 Azure 门户管理 Azure 时序见解预览版环境。 下面是使用 Azure 门户管理 **PAYG** Azure 时序见解预览版环境与管理 S1 或 S2 环境的主要区别：

* Azure 门户的“概览”边栏选项卡在 Azure 时序见解中保持不变，以下方面除外：
  * 删除了容量，因为此概念与 **PAYG** 环境无关。
  * 添加了时序 ID 属性。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将你定向到 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。
  * 列出了 Azure 存储帐户名称。

* Azure 门户的“配置”边栏选项卡已在 Azure 时序见解预览版中删除，因为 **PAYG** 环境不可配置。

* Azure 门户的“引用数据”边栏选项卡已在 Azure 时序见解预览版中删除，因为引用数据不是 **PAYG** 环境的组件。

![验证实例。][4]

## <a name="next-steps"></a>后续步骤

阅读[如何规划环境](./time-series-insights-update-plan.md)。

阅读[如何添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

阅读[如何添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
