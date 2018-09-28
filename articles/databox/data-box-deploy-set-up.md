---
title: 设置 Azure Data Box| Microsoft Docs
description: 了解 Azure Data Box 的软件和网络要求
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6623744a26d79ccbc8b68ba84baf3a37024e0ed7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952301"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>教程：为 Azure Data Box 连接电缆并连接到它

本教程介绍了如何为 Azure Data Box 连接电缆、连接到它以及将其打开。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为 Data Box 连接电缆
> * 连接到 Data Box

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：订购 Azure Data Box](data-box-deploy-ordered.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。 
3. 已看完 [Data Box 安全准则](data-box-safety.md)。
4. 已收到用于你的 100 TB 存储设备的一条接地电源线。
5. 你有一台主机，其中的数据需复制到 Data Box。 该主机必须
    - 运行[支持的操作系统](data-box-system-requirements.md)。
    - 连接到高速网络。 强烈建议你至少建立一个 10 GbE 连接。 如果 10 GbE 连接不可用，则可使用 1 GbE 数据链路，但复制速度会受影响。 
6. 必须找到一个可以放置 Data Box 的平面。 如需将设备置于标准的机架上，则需要数据中心机架中的一个 7U 槽。 可以将设备平放或直放在机架中。
7. 你已经采购了以下电缆，用于将 Data Box 连接到主机。
    - 两根 10 GbE SFP+ Twinax 铜线（用于 DATA 1、DATA 2 网络接口）
    - 一根 RJ-45 CAT 6 网线（用于 MGMT 网络接口）
    - 一根 RJ-45 CAT 6A 网线或一根 RJ-45 CAT 6 网线（用于 DATA 3 网络接口，分别配置为 10 Gbps 或 1 Gbps）

## <a name="cable-your-device"></a>连接设备电缆

执行以下步骤来连接设备电缆。

1. 检查设备是否有任何改动的痕迹或任何明显的损坏。 如果设备已被篡改或严重损坏，请不要继续操作。 请立即联系 Microsoft 支持部门，让他们帮助评估设备是否正常，或者是否需要更换磁盘。
2. 将设备运输到你希望为其通电的位置。 将设备放置在一个平面上。 也可以将设备放置在标准机架上。
3. 连接电源线和网络电缆。 对于常见配置，连接电缆后的设备的底板如下所示。 
    
    ![已连接了电缆的 Data Box 设备底板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. 将电源线连接到所标示的电源输入位置。 电源线的另一端应当连接到配电装置。
    2. 将 RJ-45 CAT 6 电缆的一端连接到 MGMT 端口，将另一端连接到笔记本电脑。            
    3. 将 RJ-45 CAT 6A 电缆的一端连接到 DATA 3 端口。 如果通过 RJ-45 CAT 6A 电缆进行连接，则 DATA 3 配置为 10 GbE，如果通过 RJ-45 CAT 6 电缆进行连接，则它配置为 1 GbE。
    4. 分别使用 10 GbE SFP + 双绞线铜缆连接 DATA 1 和 DATA 2 端口。 
    5. 数据端口上的电缆的另一端通过一台 10 GbE 交换机连接到主计算机。

4. 在设备的前操作面板上找到电源按钮。 打开设备。

    ![Data Box 电源按钮](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>连接到设备

执行以下步骤来使用本地 Web UI 和门户 UI 设置设备。

1. 在使用的笔记本上配置以太网适配器以通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到设备。 
2. 连接到设备的 MGMT 端口并访问其位于 https://192.168.100.10 的本地 Web UI。 从打开设备算起，进行此连接可能需要长达 5 分钟的时间。
3. 单击“详细信息”，然后单击“转到网页”。

   ![连接到本地 Web UI](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. 此时将显示本地 Web UI 的“登录”页面。 确保设备序列号在门户 UI 和本地 Web UI 中匹配。 设备此时处于锁定状态。
5. 登录到 [Azure 门户](https://portal.azure.com)。
6. 从门户下载设备凭据。 转到“常规”>“设备详细信息”。 复制**设备密码**。 设备密码绑定到门户中的特定订单。 

    ![获取设备凭据](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 提供你在上一步骤中从 Azure 门户获取的设备密码来登录到设备的本地 Web UI。 单击“登录”。
8. 在“仪表板”上，确保已配置了网络接口。 
    - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 
    - 如果未启用 DHCP，请转到“设置网络接口”，并根据需要分配静态 IP。

    ![设备仪表板](media/data-box-deploy-set-up/data-box-dashboard-1.png)

在设备设置完成后，可以连接到设备共享并将数据从计算机复制到设备。 

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 为 Data Box 连接电缆
> * 连接到 Data Box

请继续学习下一教程，了解如何在 Data Box 上复制数据。

> [!div class="nextstepaction"]
> [将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)

