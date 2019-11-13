---
title: 预览版环境中的数据建模-Azure 时序见解 |Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据建模。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: fb1bfb30d531f8b93489c8fc1dfbc6b8172339a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006460"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据建模

本文介绍如何在 Azure 时序见解预览版中使用时序模型。 它详细介绍了几个常见数据方案。

若要详细了解如何使用更新，请阅读 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。

## <a name="types"></a>类型

### <a name="create-a-single-type"></a>创建单个类型

1. 请参阅时序模型选择器面板，并从菜单中选择 "**类型**"。 折叠面板以重点关注时序模型类型。

    [!["类型" 窗格](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. 选择 **+ 添加**。
1. 输入与类型相关的所有详细信息，然后选择 "**创建**"。 此操作在环境中创建类型。

    [用于添加类型 ![选择](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>批量上传一个或多个类型

1. 选择“上传 JSON”。
1. 选择包含类型有效负载的文件。
1. 选择“上传”。

    [选择一种或多种类型的批量上传 ![](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>编辑单个类型

1. 选择类型，然后选择“编辑”。 
1. 进行所需的更改，然后选择“保存”。

    [用于编辑类型 ![选择](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>删除类型

1. 选择类型，然后选择“删除”。
1. 如果没有实例与类型相关联，则它已被删除。

    [!["删除" 按钮](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>层次结构

### <a name="create-a-single-hierarchy"></a>创建单个层次结构

1. 请参阅时序模型选择器面板，并从菜单中选择 "**层次结构**"。 折叠面板以重点关注时序模型层次结构。

    [!["层次结构" 窗格](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. 选择 **+ 添加**。

    [!["添加" 按钮](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. 在右窗格中选择 " **+ 添加级别**"。

    [!["添加级别" 按钮](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. 输入层次结构详细信息，然后选择“创建”。

    [![层次结构详细信息和 "创建" 按钮](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>批量上传一个或多个层次结构

1. 选择“上传 JSON”。
1. 选择包含层次结构有效负载的文件。
1. 选择“上传”。

    [用于批量上传层次结构的 ![选择](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>编辑单个层次结构

1. 选择层次结构，然后选择“编辑”。
1. 进行所需的更改，然后选择“保存”。

    [用于编辑单个层次结构 ![选择](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>删除层次结构

1. 选择层次结构，然后选择 "**删除**"。 
1. 如果没有实例与层次结构相关联，则它已被删除。

    [!["删除" 按钮](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>创建单个实例

1. 请参阅时序模型选择器面板，并从菜单中选择 "**实例**"。 折叠面板以将焦点放在时序模型实例上。

    [!["实例" 窗格](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. 选择“添加”。

    [![用于添加实例的选择](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. 输入实例详细信息，选择类型和层次结构关联，然后选择“创建”。

### <a name="bulk-upload-one-or-more-instances"></a>批量上传一个或多个实例

1. 选择“上传 JSON”。
1. 选择包含实例有效负载的文件。

    [用于批量上传一个或多个实例的 ![选择](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. 选择“上传”。

### <a name="edit-a-single-instance"></a>编辑单个实例

1. 选择实例，然后选择“编辑”。 
1. 进行所需的更改，然后选择“保存”。

    [用于编辑单个实例 ![选项](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 有关时序模型的详细信息，请参阅[数据建模](./time-series-insights-update-tsm.md)。

- 若要详细了解预览版，请阅读[在 Azure 时序见解预览版资源管理器中可视化数据](./time-series-insights-update-explorer.md)。

- 若要了解支持的 JSON 形状，请参阅[支持的 json 形状](./time-series-insights-send-events.md#supported-json-shapes)。
