---
title: 教程：安装 Azure Stack Edge 物理设备（拆包、装机和布线）| Microsoft Docs
description: 有关 Azure Stack Edge 安装的第二篇教程涉及物理设备的拆包、装机和布线。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 429fe0c4db4a7825a6a98aa5d2cd6af609a34a61
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564985"
---
# <a name="tutorial-install-azure-stack-edge"></a>教程：安装 Azure Stack Edge

本教程介绍如何安装 Azure Stack Edge 物理设备。 安装过程包括设备的拆包、装机和布线。 

安装可能需要大约两小时才能完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 拆开设备包装
> * 将设备装入机架
> * 为设备布线

## <a name="prerequisites"></a>先决条件

下面是安装物理设备所要满足的先决条件：

### <a name="for-the-azure-stack-edge-resource"></a>对于 Azure Stack Edge 资源

在开始之前，请确保：

* 已完成[准备部署 Azure Stack Edge](azure-stack-edge-deploy-prep.md) 中的所有步骤。
    * 已创建用于部署设备的 Azure Stack Edge 资源。
    * 已生成用于通过 Azure Stack Edge 资源激活设备的激活密钥。

 
### <a name="for-the-azure-stack-edge-physical-device"></a>对于 Azure Stack Edge 物理设备

在部署设备之前：

- 请确保将设备安全地放置在平坦、稳定的水平工作台面上。
- 检查进行安装的场地是否具有：
    - 从独立线路引出的标准交流电源

        \- 或 -
    - 带有不间断电源 (UPS) 的机架配电装置 (PDU)
    - 要安装设备的机架上有一个 1U 插槽

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络

开始之前：

- 查看部署 Azure Stack Edge 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Azure Stack Edge 网络要求](azure-stack-edge-system-requirements.md#networking-port-requirements)。

- 请确保最小 Internet 带宽为 20 Mbps，以便实现设备的理想运行。


## <a name="unpack-the-device"></a>拆开设备包装

此设备装运在一个箱子里。 按以下步骤拆开设备包装。 

1. 将箱子放在平坦的水平表面上。
2. 检查箱子和包装泡沫是否有破损、刮痕、水渍或任何其他明显损坏。 如果箱子或包装严重损坏，请勿打开包装。 请联系 Microsoft 支持部门，帮助你评估设备是否处于良好的工作状态。
3. 打开箱子。 打开包装箱后，请确保有：
    - 一个单机箱 Azure Stack Edge 设备
    - 两根电源线
    - 一个导轨套装件
    - 一本“安全、环保和法规信息”小册子

如果上述任一部件缺失，请联系 Azure Stack Edge 支持部门。 接下来是安装设备的机架。


## <a name="rack-the-device"></a>将设备装入机架

必须在标准的 19 英寸机架上安装设备。 使用以下过程将设备安装在标准的 19 英寸机架中。

> [!IMPORTANT]
> Azure Stack Edge 设备必须安装在机架中才能正常工作。


### <a name="prerequisites"></a>先决条件

- 在开始之前，请阅读“安全、环保和法规信息”小册子中的安全指示。 这本小册子已随设备一起交付。
- 在最靠近机箱底部的分配空间中开始安装导轨。
- 对于工具导轨安装配置：
    -  需要提供八个螺钉：#10-32、#12-24、#M5 或 #M6。 螺丝的头端直径必须小于 10 毫米（0.4 英寸）。
    -  需要一把平头螺丝刀。

### <a name="identify-the-rail-kit-contents"></a>识别导轨工具包内容

找到用于安装导轨套装件的组件：
1. 两个 A7 Dell ReadyRails II 滑轨装配件
2. 两片粘扣带

    ![识别导轨工具包内容](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>安装和拆除免工具导轨（方孔或圆孔机架）

> [!TIP]
> 此选项无需工具，因为它不需要工具即可将导轨安装到机架上的无螺纹方孔或圆孔中并可将其拆除。

1. 朝内定位好标有 **FRONT** 的左右导轨尾段并摆放好每个尾段，使之固定在立式机架法兰正面的孔位中。
2. 在所需 U 形空间的下孔和上孔中对齐每个尾段。
3. 啮合导轨的末端，使之完全进入立式机架法兰，并且闩锁卡入就位。 在立式机架法兰上重复这些步骤以定位和固定正面尾段。
4. 若要拆除导轨，请拉起尾段中间的闩锁释放按钮，并松开每个导轨。

    ![安装和拆除免工具导轨](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>安装和拆除工模导轨（螺孔机架）

> [!TIP]
> 此选项需要工具，因为它需要工具（_平头螺丝刀_）来将导轨安装到机架上的螺纹圆孔中并可将其拆除。

1. 使用一字螺丝刀拆下正面和背面安装支架上的定位销。
2. 拉起并旋转导轨闩锁附属装配件，从安装支架上将其拆下。
3. 使用四颗螺丝将左右安装导轨安装到正面的立式机架法兰上。
4. 将左右背面支架滑向背面的立式机架法兰，并使用四颗螺丝将其固定。

    ![安装和拆除工模导轨](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>在机架中安装系统

1. 朝机架外部拉动内部滑轨，直到锁定就位。
2. 在系统的每一侧找到背面导轨支架，并将其下降到滑动装配件上的背面 J 形槽中。 朝下旋转系统，直到所有导轨支架固定在 J 形槽中。
3. 向内推动系统，直到锁定杆卡入就位。
4. 按下两个导轨上的滑动/释放锁定按钮，并将系统滑入机架。

    ![在机架中安装系统](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>从机架中拆除系统

1. 在内部导轨的两侧找到锁定杆。
2. 将每个锁定杆向上旋转到释放位置，以解除锁定每个锁定杆。
3. 紧紧抓住系统的两侧并将其向前拉动，直到导轨支架滑到 J 形槽的正面。 向上抬起系统使其脱离机架，然后将其放在水平的表面上。

    ![从机架中拆除系统](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>啮合和释放撞击闩锁

> [!NOTE]
> 对于未配备撞击闩锁的系统，请根据本过程的步骤 3 中所述，使用螺丝来紧固系统。

1. 朝向正面，在系统的任一侧找到撞击闩锁。
2. 将系统推入机架时，闩锁会自动啮合；拉起闩锁会释放闩锁。
3. 若要在机架中保护系统以便于装运，或者要在其他不稳定的环境中保护系统，请在每个闩锁下面找到五金螺丝，并使用 #2 号十字螺丝刀拧紧每颗螺丝。

    ![啮合和释放撞击闩锁](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>为设备布线

敷设电缆，然后将电缆连接到设备。 以下过程说明如何为 Azure Stack Edge 设备排布电源线和网线。

在开始为设备布线之前，需要符合以下先决条件：

- 已拆包并装入机架的 Azure Stack Edge 物理设备。
- 两根电源线。
- 至少一根用于连接管理接口的 1-GbE RJ-45 网线。 设备上有两个 1-GbE 网络接口：一个用于管理，一个用于数据。
- 一根 25-GbE SFP+ 铜缆用于配置每个数据网络接口。 至少需要将一个数据网络接口（端口 2、端口 3、端口 4、端口 5 或端口 6）连接到 Internet（在连接到 Azure 的情况下）。  
- 可以取用两个配电单元（建议）。

> [!NOTE]
> - 如果仅连接一个数据网络接口，我们建议使用 25/10-GbE 网络接口（例如端口 3、端口 4、端口 5 或端口 6）将数据发送到 Azure。 
> - 为获得最佳性能并处理大量数据，请考虑连接所有数据端口。
> - Azure Stack Edge 设备应连接到数据中心网络，这样才能从数据源服务器引入数据。

在 Azure Stack Edge 设备上：

- 前面板有磁盘驱动器和电源按钮。

    - 设备正面有 10 个磁盘插槽。
    - 插槽 0 有一个 240 GB SATA 驱动器用作操作系统磁盘。 插槽 1 为空，插槽 2 到插槽 9 是用作数据磁盘的 NVMe SSD。
- 背板带有冗余电源单元 (PSU)。
- 背板有六个网络接口：

    - 两个 1-Gbps 接口。
    - 四个 25-Gbps 接口，它们也可以充当 10-Gbps 接口。
    - 主板管理控制器 (BMC)。

- 背板有两块对应于 6 个端口的网卡：

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

有关这些网卡支持的线缆、交换机和收发器的完整列表，请参阅 [Cavium FastlinQ 41000 系列互操作性矩阵](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)。
 
执行以下步骤，为设备排布电源线和网线。

1. 识别设备背板上的各个端口。

    ![已布线设备的背板](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. 在设备正面找到磁盘插槽和电源按钮。

    ![设备的前面板](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. 将电源线连接到机柜中的每个 PSU。 为确保高可用性，请安装这两个 PSU 并将其连接到不同的电源。
4. 将电源线连接到配电装置 (PDU)。 确保两个 PSU 使用单独的电源。
5. 按下电源按钮打开设备。
6. 将 1-GbE 网络接口的端口 1 连接到用于配置物理设备的计算机。 端口 1 是专用的管理接口。
7. 将一个或多个端口 2、端口 3、端口 4、端口 5 或端口 6 连接到数据中心网络/Internet。

    - 如果连接端口 2，请使用 RJ-45 网线。
    - 对于 10/25-GbE 网络接口，请使用 SFP+ 铜缆。

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Stack Edge 主题，例如如何执行以下操作：

> [!div class="checklist"]
> * 拆开设备包装
> * 将设备装入机架
> * 为设备布线

请继续学习下一教程，了解如何连接、设置和激活设备。

> [!div class="nextstepaction"]
> [连接并设置 Azure Stack Edge](./azure-stack-edge-deploy-connect-setup-activate.md)
