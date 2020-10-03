---
title: 预配和管理第 2 代环境 - Azure 时序见解 | Microsoft Docs
description: 了解如何预配和管理 Azure 时序见解第 2 代环境。
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: e54e8e9de1df4c8a1c870285d36e4580daaa698a
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667820"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>预配和管理 Azure 时序见解第 2 代

本文介绍了如何使用 [Azure 门户](https://portal.azure.com/)创建和管理 Azure 时序见解第 2 代环境。

## <a name="overview"></a>概述

预配 Azure 时序见解第 2 代环境时，你将创建以下 Azure 资源：

* 遵循即用即付定价模型的 Azure 时序见解第 2 代环境
* Azure 存储帐户
* 一个可选的暖存储，用于更快速且无限制的查询

> [!TIP]
>
> * 了解[如何规划环境](./time-series-insights-update-plan.md)。
> * 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)或如何[添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

将了解如何执行以下操作：

1. 将每个 Azure 时序见解第 2 代环境与一个事件源关联。 你还将提供时间戳 ID 属性和唯一的使用者组，以确保环境有权访问相应的事件。

1. 预配完成后，可根据业务需求修改访问策略和其他环境属性。

   > [!NOTE]
   > 预配环境时，第一步是可选的。 如果跳过此步骤，那么你以后必须向环境附加事件源，以便数据可以开始流动到你的环境中并可供用户通过查询对其进行访问。

## <a name="create-the-environment"></a>创建环境

若要创建 Azure 时序见解第 2 代环境，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中的“物联网”下创建一个 Azure 时序见解资源。

1. 选择“第 2 代(L1)”作为“层级”。 提供一个环境名称，并选择要使用的订阅组和资源组。 然后，选择一个支持的位置，用于托管环境。

   [![创建 Azure 时序见解实例。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 输入时序 ID。

    > [!NOTE]
    >
    > * 时序 ID *区分大小写*且*不可变*。 （一经设置，不可更改。）
    > * 时序 ID 最多可以是*三个*键。 可将其视为数据库中的主键，它唯一地表示会向你的环境发送数据的每个设备传感器。 它可以是一个属性，也可以是最多包含三个属性的组合。
    > * 有关详细信息，请参阅[如何选择时序 ID](time-series-insights-update-how-to-id.md)

1. 通过选择存储帐户名称、帐户种类并指定[复制](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal)选项来创建 Azure 存储帐户。 这样做会自动创建一个 Azure 存储帐户。 默认情况下将创建[常规用途 v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 帐户。 该帐户在之前选择的 Azure 时序见解第 2 代环境所在的区域中创建。
另外，在创建新的 Azure 时序第 2 代环境时，还可以通过 [ARM 模板](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)自带存储 (BYOS)。

1. **（可选）** 如果需要在环境中对最新数据进行更快且不受限制的查询，请为环境启用暖存储。 也可在创建 Azure 时序第 2 代环境后，在左导航窗格中通过“存储配置”选项创建或删除暖存储。

1. **（可选）** 可以现在就添加事件源， 也可以等到预配完实例后再添加。

   * Azure 时序见解支持使用 [Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和 [Azure 事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作为事件源选项。 虽然在创建环境时只能添加单个事件源，但可以在以后添加其他事件源。

     在添加事件源时，可以选择现有的使用者组，也可以创建新的使用者组。 请注意，事件源需要一个适合你的环境的独特使用者组才能将数据读入其中。

   * 选择相应的时间戳属性。 默认情况下，Azure 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件方案或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 在这种情况下，请确保验证你是决定使用还是不使用时间戳属性。

     [![事件源配置选项卡](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 确认已按照所需方式预配和配置了环境。

    [![“查看 + 创建”选项卡](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理环境

可以使用 Azure 门户管理 Azure 时序见解第 2 代环境。 请注意，通过 Azure 门户管理环境时，第 2 代环境与第1 代 S1 或 S2 环境之间有几个关键差异：

* Azure 门户第 2 代的“概览”边栏选项卡进行了以下更改：

  * 删除了容量，因为它不适用于第 2 代环境。
  * 添加了“时序 ID”属性****。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将您定向到 [Azure 时序见解资源管理器](./time-series-insights-update-explorer.md)。
  * 列出了 Azure 存储帐户名称。

* 删除了Azure 门户的“配置”边栏选项卡，因为缩放单元不适用于 Azure 时序见解第 2 代环境。 但是，可以使用“存储配置”来配置新引入的暖存储。****

* Azure 时序见解第 2 代中删除了 Azure 门户的“参考数据”边栏选项卡，因为参考数据概念已被[时序模型 (TSM)](/azure/time-series-insights/concepts-model-overview) 替代。

[![Azure 门户中的 Azure 时序见解第 2 代环境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 阅读[规划你的环境](./time-series-insights-update-plan.md)，详细了解 Azure 时序见解正式版环境和第 2 代环境。

* 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

* 配置 [IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
