---
title: Azure Data Box Heavy 设置教程 | Microsoft Docs
description: 了解如何为 Azure Data Box Heavy 布线并与它建立连接
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 15e031bcdcac7bd84d58286f5bdb5a7b6cd0c973
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199209"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>教程：为 Azure Data Box Heavy 布线并与它建立连接

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Azure Data Box Heavy 入门

::: zone-end

::: zone target = "docs"

本教程介绍如何为 Azure Data Box Heavy 布线、与它建立连接以及将它打开。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为 Data Box Heavy 布线
> * 连接到 Data Box Heavy

::: zone-end

::: zone target = "chromeless"

本指南提供了有关如何检查先决条件、为设备连接电缆并连接到设备、复制数据、将数据上传到 Azure，并验证上传的数据的说明。

::: zone-end

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：订购 Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)。
2. 已收到 Data Box Heavy，并且门户中的订单状态为“已送达”。 
3. 已查看 [Data Box Heavy 安全准则](data-box-safety.md)。
4. 必须在数据中心内的平坦场地上操作，该场地靠近可用的网络连接，并能够放得下 Data Box Heavy 大小的设备。 不能在机架上安装此设备。
5. 你已收到四条在存储设备中使用的已接地电源线。
6. 应该将一个主机连接到数据中心网络。 Data Box Heavy 将从此计算机复制数据。 主机必须运行[受支持的操作系统](data-box-heavy-system-requirements.md)。
7. 数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 
8. 需要使用一台配备 RJ-45 线缆的笔记本电脑连接到本地 UI 并配置设备。 使用笔记本电脑配置设备的每个节点一次。
9. 需要为每个设备节点使用一条 40-Gbps 线缆或 10-Gbps 线缆。
    - 选择与 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 网络接口兼容的线缆。
    - 40-Gbps 线缆的设备端需是 QSFP+。
    - 对于 10-Gbps 线缆，需要使用 SFP+ 线缆，其一端插入 10-Gbps 交换机，插入设备的另一端配备 QSFP+ 转 SFP+ 适配器（或 QSA 适配器）。

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>为设备排布电源线

执行以下步骤为设备布线。

1. 检查设备是否有任何改动的痕迹或任何明显的损坏。 如果设备已被篡改或严重损坏，请不要继续操作。 请立即[联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)，让他们帮助评估设备是否正常，或者是否需要更换磁盘。
2. 将设备转移到安装场地。

    ![Data Box Heavy 设备安装场地](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. 如下所示锁定设备的背面脚轮。

    ![Data Box Heavy 设备脚轮已锁定](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. 找到用于解锁设备前面和后门的旋钮。 解锁并移动前门，直到与设备侧面齐平。 对后门重复此操作。
    运行设备时，这两个门必须一直打开，使设备中保持充足的前后空气流通。

    ![Data Box Heavy 门已打开](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. 设备背面的托盘中应有四条电源线。 取出托盘中的所有线缆并将其放在一边。

    ![托盘中的 Data Box Heavy 电源线](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. 下一步是识别设备背面的各个端口。 有两个设备节点：**NODE1** 和 **NODE2**。 每个节点有四个网络接口：**MGMT**、**DATA1**、**DATA2** 和 **DATA3**。 **MGMT** 用于在初始配置设备期间配置管理功能。 **DATA1**-**DATA3** 是数据端口。 **MGMT**和 **DATA3** 端口为 1-Gbps，而 **DATA1** 和 **DATA2** 可用作 40-Gbps 或 10-Gbps 端口。 在两个设备节点的底部，是供两个设备节点共享的四块电源 (PSU)。 从此设备的正面来看，**PSU** 左到右分别为 **PSU1**、**PSU2**、**PSU3** 和 **PSU4**。

    ![Data Box Heavy 端口](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. 将所有四条电源线连接到设备电源。 绿色 LED 将会亮起并闪烁。
8. 使用前面板上的电源按钮打开设备节点。 按住电源按钮几秒钟，直到蓝色指示灯亮起。 设备背面电源的绿色 LED 现在应该稳定点亮。 设备正面操作面板还带有故障 LED。 当故障 LED 亮起时，指示 PSU 或风扇有故障，或者磁盘驱动器出现问题。  

    ![Data Box Heavy 正面操作面板](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>为第一个网络节点布线

在设备的某个节点上，执行以下步骤进行网络布线。

1. 使用 CAT 6 RJ-45 网络电缆（图片中右上角连接到标有 MGMT 的插头的电缆）将主计算机连接到 1-Gbps 管理端口。
2. 使用 QSFP+ 电缆（光纤或铜）连接至少一个 40-Gbps（首选超过 1 Gbps）的网络接口进行数据传输。 如果使用 10-Gbps 交换机，请使用 SFP+ 电缆和 QSFP+ 转 SFP+ 适配器（QSA 适配器）连接 40 Gbps 网络接口进行数据传输。

    ![Data Box Heavy 端口已布线](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 和 DATA2 已切换，与本地 Web UI 中显示的接口不相符。
    > 按如下所示插入时，将连接 40-Gbps 线缆适配器。

    ![Data Box Heavy 40-Gbps 线缆适配器](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>配置第一个节点

执行以下步骤，使用本地配置和 Azure 门户设置设备。

1. 从门户下载设备凭据。 转到“常规”>“设备详细信息”。  复制**设备密码**。 这些密码与门户中的特定订单相关。 你会看到两个设备序列号，它们对应于 Data Box Heavy 中的两个节点。 这两个节点的设备管理员密码相同。

    ![Data Box Heavy 设备凭据](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. 通过 CAT6 RJ-45 网线将客户端工作站连接到设备。
3. 在用于连接设备的计算机上，使用静态 IP 地址 `192.168.100.5` 和子网 `255.255.255.0` 配置以太网适配器。

    ![Data Box Heavy 连接到本地 Web UI](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. 通过以下 URL 连接到设备的本地 Web UI：`http://192.168.100.10`。 依次单击“高级”、“继续访问 192.168.100.10 (不安全)”。  
5. 此时将显示本地 Web UI 的“登录”  页面。
    
    - 此页面上的某个节点序列号与门户 UI 和本地 Web UI 中的序列号相匹配。 请记下节点编号与序列号的映射。 门户中有两个节点和两个设备序列号。 此映射可帮助你了解哪个节点对应于哪个序列号。
    - 设备此时处于锁定状态。
    - 提供你在上一步骤中获取的设备管理员密码，以登录到设备。 单击**登录**。

    ![登录到 Data Box Heavy 本地 Web UI](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. 在仪表板上，确保已配置网络接口。 设备节点上有四个网络接口：两个 1-Gbps 接口和两个 40-Gbps 接口。 有一个 1-Gbps 接口是管理接口，不可由用户配置。 剩余的三个网络接口专用于数据传输，可由用户配置。

- 如果你的环境中启用了 DHCP，则会自动配置网络接口。
- 如果未启用 DHCP，请转到“设置网络接口”，并根据需要分配静态 IP。

    ![Data Box Heavy 仪表板节点 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>配置第二个节点

对于设备的第二个节点，请执行[配置第一个节点](#configure-first-node)中详述的步骤。

![Data Box Heavy 仪表板节点 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

在设备设置完成后，可以连接到设备共享并将数据从计算机复制到设备。

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Data Box Heavy 主题，例如：

> [!div class="checklist"]
> * 为 Data Box Heavy 布线
> * 连接到 Data Box Heavy

请继续学习下一篇教程，了解如何复制 Data Box Heavy 上的数据。

> [!div class="nextstepaction"]
> [将数据复制到 Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
