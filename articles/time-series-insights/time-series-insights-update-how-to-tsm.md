---
title: Azure 时序见解预览版中的数据建模 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据建模。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273742"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据建模

本文档介绍了如何使用 Azure 时序见解预览版中的时序模型。 它详细介绍了几个常见数据方案。

若要详细了解如何使用更新，请阅读 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。

## <a name="types"></a>类型

### <a name="create-a-single-type"></a>创建单个类型

1. 转到“时序模型”选择器面板，并从菜单中选择“类型”。 折叠面板以集中于“时序模型”类型。

    [![创建单个类型](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. 选择 **+ 添加**。
1. 输入与类型相关的所有详细信息，然后选择“创建”。 此操作在环境中创建类型。

    [![添加类型](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>批量上传一个或多个类型

1. 选择“上传 JSON”。
1. 选择包含类型有效负载的文件。
1. 选择“上传”。

    [![上传 JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>编辑单个类型

1. 选择类型，然后选择“编辑”。 
1. 进行所需的更改，然后选择“保存”。

    [![编辑类型](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>删除类型

1. 选择类型，然后选择“删除”。
1. 如果没有实例与类型相关联，则它已被删除。

    [![删除类型](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>层次结构

### <a name="create-a-single-hierarchy"></a>创建单个层次结构

1. 转到“时序模型”选择器面板，并从菜单中选择“层次结构”。 折叠面板以集中于“时序模型”层次结构。

    [![选择层次结构](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. 选择 **+ 添加**。

    [![添加层次结构](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. 在右窗格中选择 " **+ 添加级别**"。

    [![添加级别](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. 输入层次结构详细信息，然后选择“创建”。

    [![创建级别](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>批量上传一个或多个层次结构

1. 选择“上传 JSON”。
1. 选择包含层次结构有效负载的文件。
1. 选择“上传”。

    [![批量上传层次结构](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>编辑单个层次结构

1. 选择层次结构，然后选择“编辑”。
1. 进行所需的更改，然后选择“保存”。

    [![编辑单个层次结构](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>删除层次结构

1. 选择层次结构，然后选择“删除”。 
1. 如果没有实例与层次结构相关联，则它已被删除。

    [![删除层次结构](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>创建单个实例

1. 转到“时序模型”选择器面板，并从菜单中选择“实例”。 折叠面板以集中于“时序模型”实例。

    [![创建单个实例](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. 选择 **添加** 。

    [![添加实例](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. 输入实例详细信息，选择类型和层次结构关联，然后选择“创建”。

### <a name="bulk-upload-one-or-more-instances"></a>批量上传一个或多个实例

1. 选择“上传 JSON”。
1. 选择包含实例有效负载的文件。

    [![批量上传一个或多个实例](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. 选择“上传”。

### <a name="edit-a-single-instance"></a>编辑单个实例

1. 选择实例，然后选择“编辑”。 
1. 进行所需的更改，然后选择“保存”。

    [![编辑单个实例](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 有关时序模型的详细信息，请阅读[数据建模](./time-series-insights-update-tsm.md)。

- 若要详细了解预览版，请阅读[在 Azure 时序见解预览版资源管理器中可视化数据](./time-series-insights-update-explorer.md)。

- 若要了解支持的 JSON 配置，请阅读[支持的 JSON 配置](./time-series-insights-send-events.md#supported-json-shapes)。
