---
title: 预配和管理预览版环境 - Azure 时序 | Microsoft Docs
description: 了解如何预配和管理 Azure 时序见解预览版环境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087211"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>预配和管理 Azure 时序见解预览版

本文介绍如何使用 [Azure 门户](https://portal.azure.com/)创建和管理 Azure 时序见解预览版环境。

## <a name="overview"></a>概述

Azure 时间序列见解 预览环境是即*用即付*（PAYG） 环境。

预配 Azure 时序见解预览版环境时，我们创建以下 Azure 资源：

* Azure 时序见解预览版环境  
* Azure 存储常规用途 v1 帐户
* 一个可选的暖存储，用于更快速且无限制的查询

> [!TIP]
> * 了解[如何规划环境](./time-series-insights-update-plan.md)。
> * 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)或如何[添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

将了解如何执行以下操作：

1. **（可选）** 将每个 Azure 时序见解预览版环境与一个事件源关联。 你还将提供时间戳 ID 属性和唯一的使用者组，以确保环境有权访问相应的事件。

   > [!NOTE]
   > 预配环境时，上一步是可选的。 如果跳过此步骤，那么以后必须向环境附加事件源，以便可以在环境中访问数据。

1. 预配完成后，可根据业务要求修改访问策略和其他环境属性。

## <a name="create-the-environment"></a>创建环境

若要创建 Azure 时序见解预览版环境，请执行以下操作：

1. 选择“PAYG”作为“层级”********。 提供一个环境名称，并选择要使用的订阅组和资源组。 然后，选择一个支持的位置，用于托管环境。

   [![创建 Azure 时序见解实例。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 输入时序 ID。

    > [!NOTE]
    > * 时间序列 ID 区分*大小写*且*不可变*。 （一经设置，不可更改。）
    > * 时间序列指示数最多只能*有三*个键。
    > * 有关详细信息，请参阅[如何选择时序 ID](time-series-insights-update-how-to-id.md)

1. 通过选择存储帐户名称并指定复制选项来创建 Azure 存储帐户。 这样做会自动创建 Azure 存储常规用途 v1 帐户。 该帐户在之前选择的 Azure 时序见解预览版环境所在的区域中创建。

    [![冷存储配置](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **（可选）** 如果需要在环境中对最新数据进行更快且不受限制的查询，请为环境启用暖存储。 也可在创建时序见解预览版环境后，在左导航窗格中通过“存储配置”选项创建或删除暖存储。****

    [![暖存储配置](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **（可选）** 可以现在就添加事件源， 也可以等到预配完实例后再添加。

   * 时间序列见解支持[Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和 Azure[事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作为事件源选项。 虽然在创建环境时只能添加单个事件源，但可以在以后添加其他事件源。 
   
     在添加事件源时，可以选择现有的使用者组，也可以创建新的使用者组。 最好创建唯一的使用者组，确保所有事件对 Azure 时序见解预览版环境可见。

   * 选择相应的时间戳属性。 默认情况下，Azure 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件方案或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 在这种情况下，请确保验证你是决定使用还是不使用时间戳属性。

     [![事件源配置选项卡](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 确认已按照所需方式预配和配置了环境。

    [![查看 + 创建选项卡](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理环境

可以使用 Azure 门户管理 Azure 时序见解预览版环境。 通过 Azure 门户管理环境时，要考虑到 PAYG Azure 时序见解预览环境与正式发布的 S1 或 S2 环境之间有几个关键差异：

* Azure 门户预览版的“概述”边栏选项卡进行了以下更改****：

  * 删除了容量，因为它不适用于 PAYG 环境。
  * 添加了“时序 ID”属性****。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将你定向到 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。
  * 列出了 Azure 存储帐户名称。

* 在 Azure 时序见解预览版中删除了Azure 门户的“配置”边栏选项卡，因为 PAYG 环境不可配置****。 但是，可以使用“存储配置”来配置新引入的暖存储。****

* 在 Azure 时序见解预览版中删除了 Azure 门户的“参考数据”边栏选项卡，因为参考数据不是 PAYG 环境的一部分****。

[![Azure 门户中的时序见解预览版环境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 阅读[计划环境](./time-series-insights-update-plan.md)，详细了解时序见解通常可用的环境和预览版环境。

- 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 配置[IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
