---
title: Azure 时序见解预览版中的数据建模 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据建模。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555398"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据建模

本文档介绍了如何使用 Azure 时序见解预览版中的时序模型。 它详细介绍了几个常见数据方案。

若要详细了解如何使用更新，请阅读 [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。

## <a name="types"></a>类型

### <a name="create-a-single-type"></a>创建单个类型

1. 转到“时序模型”选择器面板，并从菜单中选择“类型”。 折叠面板以集中于“时序模型”类型。

    ![Portal_one][1]

1. 选择 **添加** 。
1. 输入与类型相关的所有详细信息，然后选择“创建”。 此操作在环境中创建类型。

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>批量上传一个或多个类型

1. 选择“上传 JSON”。
1. 选择包含类型有效负载的文件。
1. 选择“上传”。

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>编辑单个类型

选择类型，然后选择“编辑”。 进行所需的更改，然后选择“保存”。

![Portal_four][4]

### <a name="delete-a-type"></a>删除类型

选择类型，然后选择“删除”。 如果没有实例与类型相关联，则它已被删除。

![Portal_five][5]

## <a name="hierarchies"></a>层次结构

### <a name="create-a-single-hierarchy"></a>创建单个层次结构

1. 转到“时序模型”选择器面板，并从菜单中选择“层次结构”。 折叠面板以集中于“时序模型”层次结构。

    ![Portal_six][6]

1. 选择 **添加** 。

    ![Portal_seven][7]

1. 在右窗格中选择“添加级别”。

    ![Portal_eight][8]

1. 输入层次结构详细信息，然后选择“创建”。

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>批量上传一个或多个层次结构

1. 选择“上传 JSON”。
1. 选择包含层次结构有效负载的文件。
1. 选择“上传”。

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>编辑单个层次结构

选择层次结构，然后选择“编辑”。 进行所需的更改，然后选择“保存”。

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>删除层次结构

选择层次结构，然后选择“删除”。 如果没有实例与层次结构相关联，则它已被删除。

![Portal_twelve][12]

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>创建单个实例

1. 转到“时序模型”选择器面板，并从菜单中选择“实例”。 折叠面板以集中于“时序模型”实例。

    ![Portal_thirteen][13]

1. 选择 **添加** 。

    ![Portal_fourteen][14]

1. 输入实例详细信息，选择类型和层次结构关联，然后选择“创建”。

### <a name="bulk-upload-one-or-more-instances"></a>批量上传一个或多个实例

1. 选择“上传 JSON”。
1. 选择包含实例有效负载的文件。

    ![Portal_fifteen][15]

1. 选择“上传”。

### <a name="edit-a-single-instance"></a>编辑单个实例

选择实例，然后选择“编辑”。 进行所需的更改，然后选择“保存”。

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>删除实例

选择实例，然后选择“删除”。 如果没有事件与实例相关联，则它已被删除。

## <a name="next-steps"></a>后续步骤

- 有关时序模型的详细信息，请阅读[数据建模](./time-series-insights-update-tsm.md)。
- 若要详细了解预览版，请阅读[在 Azure 时序见解预览版资源管理器中可视化数据](./time-series-insights-update-explorer.md)。
- 若要了解支持的 JSON 配置，请阅读[支持的 JSON 配置](./time-series-insights-send-events.md#json)。

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png