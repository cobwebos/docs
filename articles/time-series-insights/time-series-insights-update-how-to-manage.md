---
title: 预配和管理预览环境-Azure 时序 |Microsoft Docs
description: 了解如何预配和管理 Azure 时序见解预览环境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014797"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>预配和管理 Azure 时序见解预览版

本文介绍如何使用 [Azure 门户](https://portal.azure.com/)创建和管理 Azure 时序见解预览版环境。

## <a name="overview"></a>概述

Azure 时序见解预览版环境是即用即付 (PAYG) 环境。

预配 Azure 时序见解预览版环境时，会创建以下 Azure 资源：

* Azure 时序见解预览版环境  
* Azure 存储常规用途 v1 帐户
* 一个可选的热存储，可实现更快且无限制的查询
  
了解[如何规划环境](./time-series-insights-update-plan.md)。

将每个 Azure 时序见解预览环境与事件源关联。 有关详细信息，请阅读[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)和[添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。 需要在此步骤提供一个时间戳 ID 属性和一个唯一使用者组。 这样做可确保环境能够访问相应的事件。

> [!NOTE]
> 当你创建时序预览环境时，可以在预配工作流中选择前面的步骤。 但是，必须将事件源附加到环境中，以便数据可以在该环境中开始流动。

预配完成后，可根据业务要求修改访问策略和其他环境属性。

## <a name="create-the-environment"></a>创建环境

创建 Azure 时序见解预览环境：

1. 在**SKU**菜单上，选择 " **PAYG** " 按钮。 提供环境名称，并选择要使用的订阅组和资源组。 然后选择托管环境的支持位置。

   [![创建 Azure 时序见解实例。](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. 输入时序 ID。

    >[!NOTE]
    > * 时序 ID 区分大小写且不可变。 （一经设置，不可更改。）
    > * 时序 ID 最多可以是三个键。
    > * 有关选择时序 ID 的详细信息，请阅读[选择时序 ID](./time-series-insights-update-how-to-id.md)。

1. 创建一个 Azure 存储帐户，方法是选择存储帐户名称并指定复制选项。 这样做会自动创建 Azure 存储常规用途 v1 帐户。 此帐户是在与之前选择的 Azure 时序见解预览环境相同的区域中创建的。

    [![为实例创建 Azure 存储帐户](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **（可选）** 如果你希望对环境中的最新数据进行更快且不受限制的查询，请为你的环境启用热存储。 创建时序见解预览环境后，还可以通过左侧导航窗格中的 "**存储配置**" 选项创建或删除热存储。

1. **（可选）** 你现在可以添加事件源。 或者，您可以等待，直到已设置实例。

   * 时序见解支持[Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和[azure 事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作为事件源选项。 尽管在创建环境时只能添加单个事件源，但稍后可以添加其他事件源。 添加事件源时，可以选择现有使用者组，或创建新的使用者组。 最好创建一个唯一的使用者组，以确保所有事件对于 Azure 时序见解预览环境都可见。

   * 选择适当的时间戳属性。 默认情况下，Azure 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件方案或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 在这种情况下，请确保验证你决定使用还是不使用时间戳属性。

     [![“事件源”选项卡](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. 确认您的环境已设置为所需的设置。

    [![“查看 + 创建”选项卡](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>管理环境

可以使用 Azure 门户管理 Azure 时序见解预览版环境。 当你通过 Azure 门户进行管理时，你将看到即用即付 Azure 时序见解预览环境与普遍可用的 S1 或 S2 环境之间的几个重要区别：

* Azure 门户的“概览”边栏选项卡在 Azure 时序见解中保持不变，以下方面除外：
  * 由于容量不适用于即用即付环境，因此已将其删除。
  * 添加时序 ID 属性。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将你定向到 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。
  * 列出了 Azure 存储帐户名称。

* Azure 时序见解预览版中会删除 Azure 门户的 "**配置**" 边栏选项卡，因为 PAYG 环境不可配置。 但是，你可以使用**存储配置**来配置新引入的热存储。

* Azure 时序见解预览版中删除了 Azure 门户的**参考数据**边栏选项卡，因为引用数据不属于现用现付环境。

[![Azure 门户中的时序见解预览版环境](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 通过阅读[规划您的环境](./time-series-insights-update-plan.md)，了解有关时序见解的详细信息，请参阅计划环境和预览环境。

- 了解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 配置[IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
