---
title: Azure Data Box 设置教程 | Microsoft Docs
description: 了解如何为 Azure Data Box 连接电缆并连接到它
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 850144e4835b43e219fa059bbc1c92bb3ef412f4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200504"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>教程：为 Azure Data Box 连接电缆并连接到它

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>为设备连接电缆并连接到它

::: zone-end

::: zone target="docs"

本教程介绍了如何为 Azure Data Box 连接电缆、连接到它以及将其打开。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为 Data Box 连接电缆
> * 连接到 Data Box

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：订购 Azure Data Box](data-box-deploy-ordered.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。  
    - 透明袋中有一个发货标签贴在当前标签下的设备上。 请确保此标签安全，因为将使用它退回货物。
    - 欧洲的某些区域收到的设备可能会带包装盒。 请确保拆除设备的包装，并保留包装盒供回寄使用。
3. 已看完 [Data Box 安全准则](data-box-safety.md)。
4. 你已收到一根接地电源线，可以将该线用于 100 TB 存储设备。
5. 你有一台主机，其中的数据需复制到 Data Box。 该主机必须
    - 运行[支持的操作系统](data-box-system-requirements.md)。
    - 连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路，但复制速度会受影响。 
6. 必须找到一个可以放置 Data Box 的平面。 如需将设备置于标准的机架上，则需要数据中心机架中的一个 7U 槽。 可以将设备平放或直放在机架中。
7. 你已经采购了以下电缆，用于将 Data Box 连接到主机。
    - 一根或多根 10-GbE SFP+ Twinax 铜线或 SFP+ 光纤电缆（用于 DATA 1、DATA 2 网络接口）。 Data Box 使用带 PCI Express 3.0 网络接口的 Mellanox ConnectX®-3 Pro EN 双端口 10GBASE-T 适配器，因此与该接口兼容的电缆都可以用。 例如，使用 CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M 电缆进行内部测试。 有关详细信息，请参阅 [Mellanox 提供的支持的电缆和交换机的列表](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf)。
    - 一根 RJ-45 CAT 6 网线（用于 MGMT 网络接口）
    - 一根 RJ-45 CAT 6A 网线或一根 RJ-45 CAT 6 网线（用于 DATA 3 网络接口，分别配置为 10 Gbps 或 1 Gbps）

## <a name="cable-your-device"></a>连接设备电缆

执行以下步骤来连接设备电缆。

1. 检查设备是否有任何改动的痕迹或任何明显的损坏。 如果设备已被篡改或严重损坏，请不要继续操作。 请立即联系 Microsoft 支持部门，让他们帮助评估设备是否正常，或者是否需要更换磁盘。
2. 将设备运输到你希望为其通电的位置。 将设备放置在一个平面上。 也可以将设备放置在标准机架上。
3. 连接电源线和网络电缆。 对于常见配置，连接电缆后的设备的底板如下所示。 可以根据环境从其他[布线选项](data-box-cable-options.md)中进行选择。
    
    ![已连接了电缆的 Data Box 设备底板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. 将电源线连接到所标示的电源输入位置。 电源线的另一端应当连接到配电装置。
    2. 将 RJ-45 CAT 6 电缆的一端连接到 MGMT 端口，将另一端连接到笔记本电脑。            
    3. 将 RJ-45 CAT 6A 电缆的一端连接到 DATA 3 端口。 如果通过 RJ-45 CAT 6A 电缆进行连接，则 DATA 3 配置为 10 GbE，如果通过 RJ-45 CAT 6 电缆进行连接，则它配置为 1 GbE。
    4. 根据需连接的进行数据传输的网络接口的情况，最多可使用两根 10-GbE SFP+ 双绞线铜缆或 SFP+ 光纤电缆来分别连接 DATA 1 和 DATA 2 端口。 
    5. 数据端口上的电缆的另一端通过一台 10-GbE 交换机连接到主计算机。

4. 在设备的前操作面板上找到电源按钮。 打开设备。

    ![Data Box 电源按钮](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

收到设备后，需要为设备连接电缆并连接到设备。 

## <a name="cable-your-device"></a>连接设备电缆

1. 如果有证据表明设备已被串改或受损，请勿继续。 请联系 Microsoft 支持部门，让其寄送更换设备。
2. 进行设备布线时，请确保有以下电缆：
    
    - （已包括）接地电源线，规格为 10 安或更高，一端有 IEC60320 C-13 连接器，用于连接到设备。
    - 一根 RJ-45 CAT 6 网线（用于 MGMT 网络接口）
    - 两根 10 GbE SFP+ Twinax 铜线（用于 10 Gbps DATA 1、DATA 2 网络接口）
    - 一根 RJ-45 CAT 6A 网线或一根 RJ-45 CAT 6 网线（用于 DATA 3 网络接口，分别配置为 10 Gbps 或 1 Gbps）

3. 在平面上移除和放置设备。 
    
4. 进行设备布线，如下所示。  

    ![已连接了电缆的 Data Box 设备底板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 将电源线连接到设备。
    2. 使用 RJ-45 CAT 6 网线将主机连接到设备上的管理端口 (MGMT)。 
    3. 使用 SFP+ Twinax 铜线连接至少一个 10 Gbps（首选超过 1 Gbps）的网络接口，DATA 1 或 DATA 2 用于数据。 
    4. 打开设备。 电源按钮位于设备的前面板上。

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>连接到设备

执行以下步骤来使用本地 Web UI 和门户 UI 设置设备。

1. 在使用的笔记本上配置以太网适配器以通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到设备。 
2. 连接到设备的 MGMT 端口并访问其位于 https\://192.168.100.10 的本地 Web UI。 从打开设备算起，进行此连接可能需要长达 5 分钟的时间。
3. 单击“详细信息”  ，然后单击“转到网页”  。

   ![连接到本地 Web UI](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. 此时将显示本地 Web UI 的“登录”  页面。 确保设备序列号在门户 UI 和本地 Web UI 中匹配。 设备此时处于锁定状态。
5. 登录到 [Azure 门户](https://portal.azure.com)。
6. 从门户下载设备凭据。 转到“常规”>“设备详细信息”。  复制**设备密码**。 设备密码绑定到门户中的特定订单。 

    ![获取设备凭据](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 提供你在上一步骤中从 Azure 门户获取的设备密码来登录到设备的本地 Web UI。 单击“登录”  。
8. 在“仪表板”  上，确保已配置了网络接口。 
   - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 
   - 如果未启用 DHCP，请转到“设置网络接口”  ，并根据需要分配静态 IP。

     ![设备仪表板](media/data-box-deploy-set-up/data-box-dashboard-1.png)

配置数据网络接口以后，也可使用任何 DATA 1 - DATA 3 接口的 IP 地址来访问本地 Web UI `https://<IP address of a data network interface>`。 

在设备设置完成后，可以连接到设备共享并将数据从计算机复制到设备。 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>连接设备

1. 若要获取设备密码，请在 [Azure 门户](https://portal.azure.com)中转到“常规”>“设备详细信息”。 
2. 在用于连接到 Data Box 的计算机上，将静态 IP 地址 192.168.100.5 和子网 255.255.255.0 分配给以太网适配器。 通过 `https://192.168.100.10` 访问设备的本地 Web UI。 打开设备后，进行连接可能需要长达 5 分钟的时间。 
3. 从 Azure 门户使用密码登录。 此时会出现一个错误，指出网站的安全证书有问题。 按照特定于浏览器的说明转到该网页。
4. 默认情况下，10 Gbps（或 1 Gbps）数据接口的网络设置被配置为 DHCP。 如果需要，可以将该接口配置为静态，并提供一个 IP 地址。 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 为 Data Box 连接电缆
> * 连接到 Data Box

请继续学习下一教程，了解如何在 Data Box 上复制数据。

> [!div class="nextstepaction"]
> [将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)

::: zone-end

