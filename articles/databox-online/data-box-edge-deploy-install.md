---
title: 有关安装 Azure Data Box Edge 物理设备的教程 | Microsoft Docs
description: 有关安装 Azure Data Box Edge 的第二篇教程涉及到物理设备的拆包、装机和布线。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: ddcaca46a2b8f9501337b3591d6ed666876e1de9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093763"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>教程：安装 Azure Data Box Edge（预览版）

本教程介绍如何安装 Data Box Edge 物理设备。 安装过程包括设备的拆包、装机和布线。 

安装可能需要大约两小时才能完成。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 拆开设备包装
> * 将设备装入机架
> * 为设备布线

> [!IMPORTANT]
> Data Box Edge 解决方案以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

下面是安装物理设备所要满足的先决条件：

### <a name="for-the-data-box-edge-resource"></a>在 Data Box Edge 资源方面

在开始之前，请确保：

* 已完成[准备部署 Azure Data Box Edge（预览版）](data-box-edge-deploy-prep.md)中的所有步骤。
    * 已创建用于部署设备的 Data Box Edge 资源。
    * 已生成用于通过 Data Box Edge 资源激活设备的激活密钥。

 
### <a name="for-the-data-box-edge-physical-device"></a>在 Data Box Edge 物理设备方面

在部署设备之前：

- 请确保将设备安全地放置在平坦、稳定的水平工作台面上。
- 检查进行安装的场地是否具有：
    - 从独立线路引出的标准交流电源

        - 或 -
    - 带有不间断电源 (UPS) 的机架配电装置 (PDU)
    - 要安装设备的机架上有一个 1U 插槽

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络

开始之前：

- 查看部署 Data Box Edge 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Data Box Edge 网络要求](data-box-edge-system-requirements.md#networking-port-requirements)。

- 请确保最小 Internet 带宽为 20 Mbps，以便实现设备的理想运行。


## <a name="unpack-the-device"></a>拆开设备包装

此设备装运在一个箱子里。 按以下步骤拆开设备包装。 

1. 将箱子放在平坦的水平表面上。
2. 检查箱子和包装泡沫是否有破损、刮痕、水渍或任何其他明显损坏。 如果箱子或包装严重损坏，请勿打开包装。 请联系 Microsoft 支持部门，帮助你评估设备是否处于良好的工作状态。
3. 打开箱子。 打开包装箱后，请确保有：
    - 一个单机箱 Edge 设备
    - 两根电源线
    - 一个免工具的滑入式机架安装套件（随附两个滑杆和安装五金件）

如果上述任一部件缺失，请联系 Data Box Edge 支持部门。 接下来是安装设备的机架。


## <a name="rack-the-device"></a>将设备装入机架

必须在标准的 19 英寸机架上安装设备。 使用以下过程将设备安装在带有前柱和后柱的标准 19 英寸机架中。

> [!IMPORTANT]
> Data Box Edge 设备必须安装在机架中才能正常工作。


1. 拉动正面释放杆，从滑杆总成中取下内部导轨。 释放制动锁，并朝内推动中间导轨，使导轨回撤。  
    现在，内部导轨和外部导轨应已分开。

    ![安装机架导轨](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. 将外部导轨安装在机柜的竖杆上。 为帮助定位，导轨滑杆上标有“Front”（正面），这一端应安装在机箱的正面。    
   1. 找到导轨组件前部和后部的导轨销。 延展导轨，使其占满机架立柱之间的距离。 先将外部导轨安装在机架的背面。 调整背面安装支架，将其定位在背面机架安装孔的内部。   

   2. 握紧背面支架上的触发器并推动，以露出金属挂钩。 对齐并将背面支架插入安装孔，然后释放触发器。

   3. 将正面支架与安装孔对齐。

   4. 正面支架现在应固定在机架上。 （可选）如果需要，可以使用 M5 X 10L 螺丝将导轨紧固在立柱上。 

      ![安装机架导轨](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. 若要在机箱上安装内部导轨，请确保内部导轨上的锁眼与机箱一侧的定位销对齐。 确保机箱定位销的头部探出内部导轨上的锁眼。 朝机箱正面方向拉动导轨，直到导轨锁入就位并发出卡嗒声。 针对另一个内部导轨重复上述过程。 将装有内部导轨的机箱推入滑杆，以完成机架安装。

    ![安装机架导轨](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>为设备布线

以下过程说明如何为 Edge 设备排布电源线和网线。

在开始为设备布线之前，需要符合以下先决条件：

- 已拆包并装入机架的 Edge 物理设备。
- 两根电源线。 
- 至少一根用于连接管理接口的 1-GbE RJ-45 网线。 设备上有两个 1-GbE 网络接口：一个用于管理，一个用于数据。
- 一根 25-GbE SFP+ 铜缆用于配置每个数据网络接口。 至少需要将一个数据网络接口（端口 2、端口 3、端口 4、端口 5 或端口 6）连接到 Internet（在连接到 Azure 的情况下）。  
- 可以取用两个配电单元（建议）。

> [!NOTE]
> - 如果仅连接一个数据网络接口，我们建议使用 25-GbE 网络接口（例如端口 3、端口 4、端口 5 或端口 6）将数据发送到 Azure。 
> - 为获得最佳性能并处理大量数据，请考虑连接所有数据端口。
> - Edge 设备应连接到数据中心网络，这样才能从数据源服务器引入数据。 

Edge 设备有 8 块 NVMe SSD。 前面板上还有状态 LED 和电源按钮。 设备的背面带有冗余电源单元 (PSU)。 设备有六个网络接口：两个 1-Gbps 接口和四个 25-Gbps 接口。 设备包含基板管理控制器 (BMC)。 请识别设备背板上的各个端口。
 
  ![已布线设备的背板](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
执行以下步骤，为设备排布电源线和网线。

1. 将电源线连接到机柜中的每个 PSU。 为确保高可用性，请安装这两个 PSU 并将其连接到不同的电源。

2. 将电源线连接到配电装置 (PDU)。 确保两个 PSU 使用单独的电源。

3. 将 1-GbE 网络接口的端口 1 连接到用于配置物理设备的计算机。 端口 1 是专用的管理接口。

4. 将一个或多个端口 2、端口 3、端口 4、端口 5 或端口 6 连接到数据中心网络/Internet。 如果连接端口 2，请使用 RJ-45 网线。 对于 25-GbE 网络接口，请使用 SFP+ 铜缆。  


## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Edge 的主题，例如以下指导：

> [!div class="checklist"]
> * 拆开设备包装
> * 将设备装入机架
> * 为设备布线

请继续学习下一教程，了解如何连接、设置和激活设备。

> [!div class="nextstepaction"]
> [连接并设置 Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


