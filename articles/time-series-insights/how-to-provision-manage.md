---
title: 预配和管理第2代环境-Azure 时序 |Microsoft Docs
description: 了解如何预配和管理 Azure 时序见解第2代环境。
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: d067d4a7fff385deea946ffa5475e1eb83548a50
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096412"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>预配和管理 Azure 时序见解 Gen2

本文介绍如何使用[Azure 门户](https://portal.azure.com/)创建和管理 Azure 时序见解 Gen2 环境。

## <a name="overview"></a>概述

预配 Azure 时序见解 Gen2 环境时，会创建以下 Azure 资源：

* 遵循即用即付定价模型的 Azure 时序见解 Gen2 环境
* Azure 存储帐户
* 一个可选的暖存储，用于更快速且无限制的查询

> [!TIP]
> * 了解[如何规划环境](./time-series-insights-update-plan.md)。
> * 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)或如何[添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

将了解如何执行以下操作：

1.  将每个 Azure 时序见解第2代环境与事件源关联。 你还将提供时间戳 ID 属性和唯一的使用者组，以确保环境有权访问相应的事件。

1. 完成预配后，可以根据业务需求修改访问策略和其他环境属性。

   > [!NOTE]
   > 在设置环境时，第一步是可选的。 如果跳过此步骤，则必须在以后将事件源附加到环境中，以便数据可以开始流入你的环境，并且可以通过查询进行访问。

## <a name="create-the-environment"></a>创建环境

创建 Azure 时序见解第2代环境：
1. 在[Azure 门户](https://portal.azure.com/)上的*物联网*下创建 Azure 时序见解资源。

1. 选择**Gen2 （L1）** 作为**层**。 提供一个环境名称，并选择要使用的订阅组和资源组。 然后，选择一个支持的位置，用于托管环境。

   [![创建 Azure 时序见解实例。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 输入时序 ID。

    > [!NOTE]
    > * 时序 ID*区分大小写*且*不可变*。 （一经设置，不可更改。）
    > * 时序 Id 最多可包含*三个*键。 将其视为数据库中的主键，这会唯一代表将数据发送到您的环境的每个设备传感器。 它可以是一个属性或最多三个属性的组合。
    > * 有关详细信息，请参阅[如何选择时序 ID](time-series-insights-update-how-to-id.md)

1. 通过选择存储帐户名称、帐户种类并指定[复制](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal)选项来创建 Azure 存储帐户。 这样做会自动创建 Azure 存储帐户。 默认情况下，将创建[常规用途 v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview)帐户。 该帐户在与之前选择的 Azure 时序见解 Gen2 环境相同的区域中创建。 或者，在创建新的 Azure 时序 Gen2 环境时，还可以通过[ARM 模板](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)引入自己的存储（BYOS）。 

1. **（可选）** 如果需要在环境中对最新数据进行更快且不受限制的查询，请为环境启用暖存储。 创建 Azure 时序见解 Gen2 环境后，还可以通过左侧导航窗格中的 "**存储配置**" 选项创建或删除热存储。

1. **（可选）** 可以现在就添加事件源， 也可以等到预配完实例后再添加。

   * Azure 时序见解支持[Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和[azure 事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作为事件源选项。 虽然在创建环境时只能添加单个事件源，但可以在以后添加其他事件源。 
   
     在添加事件源时，可以选择现有的使用者组，也可以创建新的使用者组。 请注意，事件源需要一个适用于您的环境的唯一使用者组才能向其中读取数据。

   * 选择相应的时间戳属性。 默认情况下，Azure 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件方案或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 在这种情况下，请确保验证你是决定使用还是不使用时间戳属性。

     [![事件源配置选项卡](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 确认已按照所需方式预配和配置了环境。

    [![查看 + 创建选项卡](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理环境

可以使用 Azure 门户来管理 Azure 时序见解 Gen2 环境。 当你通过 Azure 门户管理环境时，Gen2 环境与 Gen1 S1 或 Gen1 S2 环境之间有几个重要区别：

* Azure 门户 Gen2**概述**"边栏选项卡包含以下更改：

  * 由于容量不适用于 Gen2 环境，因此已将其删除。
  * 添加了“时序 ID”属性****。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将您定向到[Azure 时序见解资源管理器](./time-series-insights-update-explorer.md)。
  * 列出了 Azure 存储帐户名称。

* 由于缩放单位不适用于 Azure 时序见解 Gen2 环境，因此删除了 Azure 门户的 "**配置**" 边栏选项卡。 但是，可以使用“存储配置”来配置新引入的暖存储。****

* Azure 门户的**引用数据**边栏选项卡在 Azure 时序见解 Gen2 中被删除，因为引用数据概念已替换为[时序模型（TSM）](./time-series-insights-update-how-to-tsm.md)。

[![Azure 门户中的 Azure 时序见解 Gen2 环境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 阅读[规划您的环境](./time-series-insights-update-plan.md)，详细了解 Azure 时序见解（一般可用环境和 Gen2 环境）。

- 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 配置[IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
