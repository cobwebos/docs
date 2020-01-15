---
title: 教程：安装 Azure FXT Edge Filer 物理设备
description: 如何为 Microsoft Azure FXT Edge Filer 混合存储缓存的物理设备组件拆除包装、将其装入机架和为其布线连接
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551023"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>教程：安装 Azure FXT Edge Filer 

本教程介绍如何为 Azure FXT Edge Filer 混合存储缓存安装硬件节点。 需要安装至少三个硬件节点来创建一个 Azure FXT Edge Filer 群集。

安装过程涉及为设备拆除包装、将其装入机架、安装电缆管理臂 (CMA) 和前挡板。 有一个单独的教程介绍了如何安装网络电缆和连接电源。 

安装 Azure FXT Edge Filer 节点需要大约一小时的时间。 

本教程包括以下设置步骤： 

> [!div class="checklist"]
> * 拆开设备包装
> * 将设备装入机架
> * 安装前挡板（可选）

## <a name="installation-prerequisites"></a>安装的先决条件 

开始安装之前，请确保要使用的数据中心和机架具有以下功能：

* 要安装设备的机架上有一个 1U 插槽。
* 有能够满足 Azure FXT Edge Filer 需求的 AC 电源和散热系统。 （阅读[电源和热规范](fxt-specs.md#power-and-thermal-specifications)，这有助于规划安装和确定安装规模。）  

  > [!NOTE] 
  > 若要充分利用多出的两个电源设备 (PSU)，在连接 AC 电源时，可将配电装置分别用于两个不同的分支线路。 阅读[连接电源线](fxt-network-power.md#connect-power-cables)，了解详细信息。  

## <a name="unpack-the-hardware-node"></a>为硬件节点拆除包装 

每个 Azure FXT Edge Filer 节点使用单独的箱子包装和发运。 完成以下步骤，拆除设备的包装。

1. 将箱子放在平坦的水平表面上。

2. 检查箱子和包装泡沫是否有破损、刮痕、水渍或任何其他明显损坏。 如果箱子或包装严重损坏，请勿打开包装。 请联系 Microsoft 支持部门，帮助你评估设备是否处于良好的工作状态。

3. 打开箱子。 确认箱子里包含以下物件：
   * 一台单机箱 FXT 设备
   * 两根电源线
   * 一个前挡板和钥匙
   * 一个导轨套装件
   * 一根电缆管理臂 (CMA)
   * CMA 安装说明小册子
   * 机架安装说明小册子
   * 一本“安全、环保和法规信息”小册子

如果上述任一物件缺失，请联系设备供货商寻求支持。 

请确保在安装设备或为设备通电之前，给设备足够时间使其温度达到室温。 如果发现设备有任何部位存在冷凝现象，请等待至少 24 小时后再安装。

接下来是安装设备的机架。

## <a name="rack-the-device"></a>将设备装入机架

Azure FXT Edge Filer 设备必须安装在标准的 19 英寸机架中。 

Azure FXT Edge Filer 混合存储缓存由三个或多个 Azure FXT Edge Filer 设备组成。 为作为系统一部分的每台设备执行一遍机架安装步骤。 

### <a name="rack-install-prerequisites"></a>机架安装先决条件

* 在开始安装机架之前，请阅读随设备装运的“安全、环保和法规信息”小册子中的安全指示。

  > [!NOTE]
  > 每次抬起节点设备 - 包括在机架中安装设备或从机架中移除设备时，始终使用两名人员来共同操作。 

* 确定要用于设备机架的导轨安装类型。 
  * 对于方孔或圆孔卡入式机架，请按照免工具导轨说明操作。
  * 对于螺纹孔机架，请按照工模导轨说明进行操作。 
  
    若要装配工模导轨，需要提供八颗螺丝，其类型为 10-32、12-24、M5 或 M6。 螺丝的头端直径必须小于 10 毫米（0.4 英寸）。

### <a name="identify-the-rail-kit-contents"></a>识别导轨工具包内容

找到用于安装导轨套装件的组件：

1. 两个 A7 Dell ReadyRails II 滑轨装配件 (1)
1. 两片粘扣带 (2)

![带编号的导轨工具包内容绘制图](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>导轨装配 - 免工具导轨（方孔或圆孔机架）

对于带卡入式方孔或圆孔的机架，请按照以下过程装配和安装导轨。 

1. 将带有“FRONT”  标记的左、右侧导轨尾段面朝内放置。 使每个尾段嵌入垂直机架法兰正面上的孔内。 (1)

2. 将每个尾段对齐机架底部和顶部孔中要安装部件的位置。 (2)

3. 啮合导轨的末端，使之完全进入立式机架法兰，并且闩锁卡入就位。 在立式机架法兰上重复这些步骤以定位和固定正面尾段。 (3)

> [!TIP]
> 若要拆除导轨，请拉起尾段中间的闩锁释放按钮 (4)，并松开每个导轨。

![免工具导轨安装和拆卸图（带步骤编号）](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>导轨装配 - 工模导轨（螺孔机架）

对于带螺孔的机架，请按照以下过程装配和安装导轨。

1. 使用一字螺丝刀拆下正面和背面安装支架上的定位销。 (1)
1. 拉起并旋转导轨闩锁附属装配件，从安装支架上将其拆下。 (2)
1. 使用四颗螺丝将左右安装导轨安装到正面的立式机架法兰上。 (3)
1. 将左右背面支架滑向背面的立式机架法兰，并使用四颗螺丝将其固定。 (4)

![工模工具导轨安装和拆卸图（带步骤编号）](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>在机架中安装系统

按照以下步骤将 Azure FXT Edge Filer 设备装入机架。

1. 朝机架外部拉动内部滑轨，直到锁定就位。 (1)
1. 在设备的每一侧找到背面导轨支架，并将其下降到滑动装配件上的背面 J 形槽中。 (2) 
1. 朝下旋转设备，直到所有导轨支架固定在 J 形槽中。 (3)
1. 向内推动设备，直到锁定杆卡入就位。
1. 按下两个导轨上的滑动/释放锁定按钮 (4)，并将设备滑入机架。

![在机架中安装系统的图示（带步骤编号）](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>从机架中拆除系统

若要从机架中拆除设备，请执行以下步骤。 

1. 在内部导轨的两侧找到锁定杆 (1)。
2. 将每个锁定杆向上旋转到释放位置，以解除锁定每个锁定杆 (2)。
3. 紧紧抓住系统的两侧并将其向前拉动，直到导轨支架滑到 J 形槽的正面。 向上抬起系统使其脱离机架，然后将其放在水平的表面上 (3)。

![从机架中拆除系统的图示（带步骤编号）](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>锁定撞击式闩锁

1. 朝向正面，在系统的任一侧找到撞击式闩锁 (1)。
2. 将系统推入机架时，闩锁会自动啮合。 

在拆除系统时，若要释放闩锁，请将其拉起 (2)。

随系统提供了可选的硬装螺丝，用于在装运系统前或在其他不稳定的环境中将系统安装到机架上。 在每个闩锁下面找到五金螺丝，并使用 #2 号十字螺丝刀拧紧每颗螺丝 (3)。

![撞击式闩锁锁定和释放图示（带步骤编号）](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>安装电缆管理臂 

FXT Edge Filer 随附有可选的电缆管理臂 (CMA)。 产品包装内附有管理臂的安装说明书。 

1. 拆除包装并确认电缆管理臂套件中的组件：
   * CMA 托盘 (1)
   * CMA (2)
   * 尼龙电缆束带 (3)
   * CMA 连接支架 (4)
   * 状态指示器电缆 (5) 

   > [!TIP] 
   > 若要出于装运目的在机架中固定 CMA，请将束带同时环绕支架和托盘，并将束带束紧。 在不稳定的环境中采用这种方式来固定 CMA 同时可以稳固系统。

   ![CMA 部件图示](media/fxt-install/cma-kit-400.png)

2. 安装 CMA 托盘。

   CMA 托盘为 CMA 提供支撑并充当其固定器。 

   1. 将托盘的每一侧与导轨内边缘的接收支架对齐并锁定。 
   1. 向前推托盘，直到其卡入到位。 (1)
   1. 若要拆除托盘，向中心挤压闩锁释放按钮，将托盘拉出接收支架 (2)。

   ![CMA 托盘安装图示](media/fxt-install/cma-tray-install-400.png)

3. 安装 CMA 连接支架。 

   > [!NOTE]
   >
   > * 根据线缆在系统中的路径，可将 CMA 连接到右或左安装轨道。 
   > * 为方便起见，可将 CMA 安装到与电源相对的位置（A 侧）。 如果安装到 B 侧，就得断开 CMA 的连接才能拆除外侧电源。 
   > * 请始终在拆除电源之前先拆除托盘。 

   ![CMA 支架安装图示](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. 为要安装 CMA 的那一侧（B 侧或 A 侧）选择合适的 CMA 连接支架。
   1. 利用滑轨背面相应的 A 侧或 B 侧标记来安装 CMA 连接支架。
   1. 将支架上的孔对准滑轨上的固定销。 按下支架，直到其锁定到位。 

4. 安装 CMA。

   1. 在系统背面，将 CMA 前端的闩锁卡入滑轨组件最内侧的支架，直到闩锁啮合 (1)。 
   1. 将另一个闩锁卡入最外侧的支架末端，直至闩锁啮合 (2)。 
   1. 若要拆除 CMA，按压内部和外部闩锁罩 (3) 顶部的 CMA 释放按钮，释放两个闩锁。

   ![主 CMA 安装图示](media/fxt-install/cma-install-400.png)

   出于访问和维修系统的目的，可旋转 CMA 使其远离系统。 在铰链端，将 CMA 提离托盘将其卸下 (1)。 待其与托盘分离后，摆动 CMA 使其远离系统 (2)。

   ![出于维修目的将 CMA 旋转打开的图示](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>安装前挡板（可选）

本部分介绍如何安装和拆除 Azure FXT Edge Filer 硬件的前挡板（面板）。 

安装前挡板： 

1. 找到并取下挡板包中随附的挡板钥匙。 
1. 将挡板与机箱前部对齐，将挡板右侧的销插入节点右侧机架装载法兰上的孔内。 
1. 将挡板左端嵌入机箱。 按压挡板直至左侧按钮卡入到位。
1. 用钥匙锁住挡板。

拆除前挡板： 
1. 使用挡板钥匙为挡板解锁。
1. 按压左侧的释放按钮，将挡板的左端从机箱拉出。
1. 将右端脱离挂钩，然后移除挡板。
   
   ![图片显示了挡板左侧的释放按钮以及如何通过将挡板从左侧向外拉来移除它](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>后续步骤

为设备拆除包装并将其装入机架后，继续进行安装：接入网络电缆，将 AC 电源接入 Azure FXT Edge Filer。

> [!div class="nextstepaction"]
> [连接网络端口电缆并供电](fxt-network-power.md)