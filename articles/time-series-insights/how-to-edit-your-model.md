---
title: 第 2 代环境中的数据建模 - Azure 时序见解 | Microsoft Docs
description: 了解 Azure 时序见解第 2 代中的数据建模。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: cac4234d5bd93de8b059ea53a4697dae1bf5c64c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667873"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Azure 时序见解第 2 代中的数据建模

本文介绍如何使用 Azure 时序见解第 2 代中的时序模型。 它详细介绍了几个常见数据方案。

> [!TIP]
>
> * 阅读有关[时序模型](concepts-model-overview.md)的详细内容。
> * 详细了解如何导航 [Azure 时序见解 Gen2 资源管理器](./time-series-insights-update-explorer.md)。

## <a name="instances"></a>Instances

Azure 时序见解资源管理器在浏览器中支持实例“创建”、“读取”、“更新”和“删除”操作****************。

若要开始，请从 Azure 时序见解资源管理器“分析”视图中选择“模型”视图 。

### <a name="create-a-single-instance"></a>创建单个实例

1. 转到“时序模型”选择器面板，并从菜单中选择“实例”****。 随即将显示与所选 Azure 时序见解环境关联的所有实例。

    [![首先通过选择实例来创建单个实例。](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. 选择“+ 添加”  。

    [![通过选择“+ 添加”按钮添加一个实例。](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. 输入实例详细信息，选择类型和层次结构关联，然后选择“创建”****。

### <a name="bulk-upload-one-or-more-instances"></a>批量上传一个或多个实例

> [!TIP]
> 可以以 JSON 格式将实例保存到桌面。然后，可以通过以下步骤上传下载的 JSON 文件。

1. 选择“上传 JSON”。****
1. 选择包含实例有效负载的文件。

    [![通过 JSON 批量上传实例。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. 选择“上传”。

### <a name="edit-a-single-instance"></a>编辑单个实例

1. 选择该实例，然后选择“编辑”或“铅笔图标”********。
1. 进行所需的更改，然后选择“保存”。****

    [![编辑单个实例。](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>删除实例

1. 选择该实例，然后选择“删除”或“垃圾桶图标”********。

   [![通过选择“删除”来删除实例。](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. 通过选择“删除”来确认删除****。

> [!NOTE]
> 实例必须成功通过字段验证检查才能删除。

## <a name="hierarchies"></a>层次结构

Azure 时序见解资源管理器在浏览器中支持层次结构“创建”、“读取”、“更新”和“删除”操作****************。

若要开始，请从 Azure 时序见解资源管理器“分析”视图中选择“模型”视图 。

### <a name="create-a-single-hierarchy"></a>创建单个层次结构

1. 转到“时序模型”选择器面板，并从菜单中选择“层次结构”****。 随即将显示与所选 Azure 时序见解环境关联的所有层次结构。

    [![通过窗格创建层次结构。](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. 选择“+ 添加”  。

    [![层次结构的“+ 添加”按钮。](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. 在右窗格中选择“+ 添加级别”****。

    [![向层次结构添加级别。](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. 输入层次结构详细信息，然后选择“保存”****。

    [![指定层次结构详细信息。](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>批量上传一个或多个层次结构

> [!TIP]
> 可以以 JSON 格式将层次结构保存到桌面。然后，可以通过以下步骤上传下载的 JSON 文件。

1. 选择“上传 JSON”。****
1. 选择包含层次结构有效负载的文件。
1. 选择“上传”。

    [![适用于批量上传层次结构的选择。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>编辑单个层次结构

1. 选择该层次结构，然后选择“编辑”或“铅笔图标”********。
1. 进行所需的更改，然后选择“保存”。****

    [![适用于编辑单个层次结构的选择。](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>删除层次结构

1. 选择该层次结构，然后选择“删除”或“垃圾桶图标”********。

    [![通过选择“删除”按钮删除层次结构。](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. 通过选择“删除”来确认删除****。

## <a name="types"></a>类型

Azure 时序见解资源管理器在浏览器中支持类型“创建”、“读取”、“更新”和“删除”操作****************。

若要开始，请从 Azure 时序见解资源管理器“分析”视图中选择“模型”视图 。

### <a name="create-a-single-type"></a>创建单个类型

1. 转到“时序模型”选择器面板，并从菜单中选择“类型”****。 随即将显示与所选 Azure 时序见解环境关联的所有类型。

    [![时序模型类型窗格。](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. 选择“+ 添加”以显示“添加新类型”弹出模型。********
1. 输入类型的属性和变量。 输入后，选择“保存”****。

    [![用于添加类型的配置设置。](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>批量上传一个或多个类型

> [!TIP]
> 可以以 JSON 格式将类型保存到桌面。然后，可以通过以下步骤上传下载的 JSON 文件。

1. 选择“上传 JSON”。****
1. 选择包含类型有效负载的文件。
1. 选择“上传”。

    [![批量类型上传选项。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>编辑单个类型

1. 选择该类型，然后选择“编辑”或“铅笔图标”********。
1. 进行所需的更改，然后选择“保存”。****

    [![在窗格中编辑类型。](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>删除类型

1. 选择该实例，然后选择“删除”或“垃圾桶图标”********。

   [![通过选择“删除”来删除类型。](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. 通过选择“删除”来确认删除****。

## <a name="next-steps"></a>后续步骤

* 有关时序模型的详细信息，请阅读[数据建模](./concepts-model-overview.md)。

* 若要了解有关 Gen2 的详细信息，请参阅 [Azure 时序见解 Gen2 资源管理器中的可视化数据](./time-series-insights-update-explorer.md)。

* 若要了解支持的 JSON 形状，请阅读[支持的 JSON 形状](./time-series-insights-send-events.md#supported-json-shapes)。
