---
title: 如何使用 Azure 门户调用直接方法
description: 本文概述如何使用 Azure 门户调用直接方法。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 9d784e1697dfbcbfec509c1a51c9b832b533c97b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87830744"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>如何使用 Azure 门户调用直接方法

借助 IoT 中心，你可以从云中对边缘设备调用[直接方法](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)。 IoT Edge 上的实时视频分析 (LVA) 模块公开了多种[直接方法](./direct-methods.md)，这些方法可以用于定义、部署和实例化用于分析实时视频的不同工作流。

本文介绍如何通过 Azure 门户对 IoT Edge 的实时视频分析模块调用直接方法调用。

## <a name="prerequisites"></a>先决条件

* 使用“[快速入门：IoT Edge 上的实时视频分析](./get-started-detect-motion-emit-events-quickstart.md)”中介绍的方法或使用[门户](./deploy-iot-edge-device.md)，在边缘设备上运行 IoT Edge 上的实时视频分析模块。

* 了解[实时视频分析](./overview.md)和[媒体图概念](./media-graph-concept.md)。

## <a name="invoking-direct-methods-via-azure-portal"></a>通过 Azure 门户调用直接方法

LVA 模块公开的每种[直接方法](./direct-methods.md)均可通过 Azure 门户进行调用。 下面的步骤提供了调用一种直接方法的详细操作。 你可以使用类似的步骤调用其他直接方法。 但是，每种直接方法都需要一个特定的 JSON 正文。

使用 `GraphTopologyList` 方法调用，检索当前部署在IoT Edge 上的实时视频分析模块上的所有图形拓扑列表。 使用以下步骤来调用此直接方法：

1. 登录 Azure 门户
1. 从门户主页找到相关的资源组来定位 IoT 中心，或者如果你知道自己的 IoT 中心，请选择它。
    ![门户主页中的资源组](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. 在 IoT 中心页上，选择“自动设备管理”下的“IoT Edge”以列出各个设备 ID。 选择相关设备 ID 以列出该设备上运行的模块。
    ![iot 中心页](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. 选择 IoT Edge 上的实时视频分析模块以打开其配置页面。<br><br>
    ![选择 IoT Edge 上的实时视频分析模块以打开其配置页面](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. 选择“直接方法”菜单选项。 <br><br>
    ![单击“直接方法”菜单选项](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > 如当前页面所示，你可能需要在连接字符串部分中添加一个值。 你无需隐藏或更改“设置名称”部分中的任何内容。 这些内容可以公开。

1. 在“方法名称”字段中，键入“GraphTopologyList”。
1. 将下面的 JSON 复制并粘贴到“有效负载”字段中。
    ```json
    {
    "@apiVersion":
    }
    ```
1. 选择页面顶部的“调用方法”按钮。<br><br>
    ![“调用方法”按钮](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. 你应该会在“结果”区域中看到一条状态 200 消息。<br><br>
    ![连接超时](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>响应

| 条件             | 状态代码 | 详细错误代码 |
|-----------------------|-------------|---------------------|
| Success               | 200         | 空值                 |
| 一般用户错误   | 400 range   |                     |
| 一般服务器错误 | 500 range   |                     |

## <a name="next-steps"></a>后续步骤

可在[直接方法](./direct-methods.md)页上找到更多直接方法。

> [!NOTE]
> 图形实例可实例化特定拓扑，因此在创建图形实例之前，请确保已设置正确的拓扑。

[快速入门：检测运动并发出事件](./get-started-detect-motion-emit-events-quickstart.md)是很好的参考文档，它可以帮助你了解要进行的直接方法调用的确切顺序。