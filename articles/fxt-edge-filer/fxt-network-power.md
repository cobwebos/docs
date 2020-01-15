---
title: 教程：将电缆连接到 Azure FXT Edge Filer
description: 如何连接 Microsoft Azure FXT Edge Flier 硬件的网络端口和电源
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550938"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>教程：为 Azure FXT Edge Filer 节点建立网络连接并提供电源

本教程将介绍如何为 Azure FXT Edge Filer 硬件节点实现网络连接。

本教程介绍： 

> [!div class="checklist"]
> * 如何为你的环境选择网络电缆类型
> * 如何将 Azure FXT Edge Filer 节点连接到数据中心网络
> * 如何通过电缆管理臂（CMA）布线
> * 如何将电源连接到机架设备并打开电源

## <a name="prerequisites"></a>必备条件

在开始本教程之前，应在标准设备机架中安装 Azure FXT Edge Filer。 CMA 应安装在文件管理器节点上。 

## <a name="identify-ports"></a>识别端口

识别 Azure FXT Edge Filer 背面的各种端口。 
 
![已布线设备的背板](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>为设备布线

* 如[网络端口](#network-ports)中所述，将 RJ-45 端口连接到数据中心的网络源。  
* 使用安全的 DHCP 服务器将 [iDRAC 端口](#idrac-port)安全地连接到单独的网络。 
* 使用 USB 端口和 VGA 端口将键盘和显示器连接到节点以进行初始设置。 需启动节点并[设置初始密码](fxt-node-password.md)才能激活该节点的其他端口。 参阅[设置初始密码](fxt-node-password.md)以了解详细信息。 

本文还介绍了如何为节点[连接交流电源](#connect-power-cables)。 

本文还介绍了如何连接到节点的[串行端口](#serial-port-only-when-necessary)，以便在有必要时进行专门的故障排除。 

### <a name="network-ports"></a>网络端口 

每个 Azure FXT Edge Filer 节点都包含以下网络端口： 

* 六个高速 25GbE/10GbE 双速率数据端口： 

  * 四个端口由两个双端口插入式网络适配器提供
  * 两个端口由主板夹层网络适配器提供 

* 两个 1GbE 端口由主板夹层网络适配器提供 

高速 25GbE/10GbE 数据端口具有兼容 SFP28 的标准插槽。 要使用光缆，必须安装 SFP28 光学收发器模块（未提供）。

1GbE 端口具有标准 RJ-45 连接器。

有关支持的电缆、开关和收发器的完整列表，请参阅 [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)（Cavium FastlinQ 41000 系列互操作性矩阵）。

用于系统的连接类型取决于数据中心环境。

* 如果连接到 25GbE 网络，请使用以下一种类型的电缆连接每个高速数据端口：

  * 光缆和 SFP28 光纤收发器，具有 25GbE 或双速率 25GbE/10GbE 功能
  * SFP28 型 25GbE 型直接连接双轴电缆

* 如果连接到 10GbE 网络，请使用以下一种类型的电缆连接每个高速数据端口： 

  * 光缆和 SFP28 光纤收发器，具有 10GbE 或双速率 25GbE/10GbE 功能。
  * SFP28 型 25GbE 型直接连接双轴电缆
  * SFP28 型 10GbE 型直接连接双轴电缆

* 1GbE 网络端口用于群集管理流量。 如果要为群集配置创建物理上独立的网络（请参阅[配置管理网络](fxt-cluster-create.md#configure-the-management-network)），请在创建群集时选中“使用 1Gb 管理网络”选项。  使用标准 Cat5 或更好的电缆连接端口，如支持的电缆列表中所述。

  如果计划使用高速端口处理所有流量，可以不连接 1GbE 端口。 默认情况下，如果有更高速数据端口可用，就不会使用 1GbE 网络端口。  

### <a name="idrac-port"></a>iDRAC 端口  

标记为 iDRAC 的端口是 1Gb 连接，允许与用于硬件管理和监视的远程访问控制器进行通信。 FXT 软件对此控制器使用智能平台管理接口（IPMI）以进行故障排除和恢复。 可以使用内置 [iDRAC 接口](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/)通过此端口监视硬件。 默认情况下启用 iDRAC 和 IPMI 访问。 

> [!Note]
> iDRAC 端口可以跳过操作系统，直接与节点上的硬件交互。 

连接和配置 iDRAC 端口时，请使用以下安全策略：

* 仅将 iDRAC 端口连接到与用于访问群集的数据网络从物理上分隔的网络。
* 在每个节点上设置安全的 iDRAC 管理员密码。 需设置此密码才能激活硬件 - 请按照[设置硬件密码](fxt-node-password.md)中的说明进行操作。
* 默认的 iDRAC 端口配置使用 DHCP 和 IPv4 进行 IP 地址分配。 确保 DHCP 环境受到良好保护，并且 DHCP 客户端和 DHCP 服务器之间的连接受到限制。 （群集控制面板包含在创建群集后更改节点地址配置方法的设置。）
* 将 iDRAC 端口设置为“专用模式”（默认设置），这会将 iDRAC/IPMI 网络流量限制于专用 RJ-45 端口。

IDRAC 端口不需要高速网络连接。
  
### <a name="serial-port-only-when-necessary"></a>串行端口（仅在必要时）

在某些情况下，Microsoft 服务和支持可能会请你将终端连接到节点的串行端口以诊断问题。  

若要连接控制台，请进行以下操作：

1. 找到 FXT Edge Filer 节点后部的串行（COM1）端口。
1. 使用零调制解调器电缆将串行端口连接到为 ANSI-115200-8N1 配置的终端。
1. 登录控制台并按照支持人员的指示采取其他操作。

## <a name="route-cables-in-the-cable-management-arm-cma"></a>在电缆管理臂（CMA）中布设电缆

每个 Azure FXT Edge Filer 节点都配有可选的电缆管理臂。 CMA 简化了电缆布线，并且无需断开电缆即可轻松访问机箱背面。 

按照以下说明将电缆穿过 CMA： 

1. 使用提供的绑带，在电缆进入和离开束线框时将电缆捆在一起，这样它们就不会干扰相邻的系统 (1)。
1. 使 CMA 处于工作位置，将电缆束穿过内框和外框 (2)。
1. 使用束线框两端预先安装好的钩环带固定电缆 (3)。
1. 将 CMA 旋转回托盘上的位置 (4)。
1. 将状态指示灯电缆安装在系统背面，并将其穿过 CMA 以进行固定。 将电缆的另一端附到 CMA 外框的一角 (5)。 

   > [!CAUTION]
   > 为避免突出的电缆可能造成的损坏，在将此电缆穿过 CMA 后，如果状态指示灯电缆有任何松弛，请将其固定。 

![已安装电缆的 CMA 的示意图](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  如果未安装 CMA，请使用导轨套件中提供的两个钩环带在系统背面布置电缆。
> 
>  1. 在两个机架法兰的内侧找到外部 CMA 支架。
>  2. 轻轻绑好线缆，将其拉到左侧。请不要碰到系统连接器。
>  3. 在系统两侧的外部 CMA 支架上的工模槽中合上粘扣带，以紧固绑在一起的线缆。
> 
>     ![无 CMA 布线的电缆](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>关于 IP 地址要求

对于 Azure FXT Edge Filer 混合存储缓存中的硬件节点，IP 地址由群集软件管理。

每个节点至少需要一个 IP 地址，但在向群集添加或删除节点时会分配节点地址。 

所需的 IP 地址总数取决于组成缓存的节点数。 

安装节点后，使用控制面板软件配置 IP 地址范围。 若要了解详细信息，请参阅[收集群集信息](fxt-cluster-create.md#gather-information-for-the-cluster)。  

## <a name="connect-power-cables"></a>连接电源线

每个 Azure FXT Edge Filer 节点使用两个电源单元（PSU）。 

> [!TIP] 
> 要利用这两个冗余 PSU，请将用交流电源线将它们分别连接到独立分支电路上的配电单元（PDU）。  
> 
> 可以使用 UPS 为 PDU 供电以获得额外保护。 

1. 将附带的电源线连接到机箱中的 PSU。 确保电源线和 PSU 固定牢固。 
1. 将电源线连接到设备机架上的配电单元。 如果可能，请将两根电线连接到两个单独的电源。 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>打开 Azure FXT Edge Filer 节点的电源

要打开节点电源，请按系统正面的电源按钮。 按钮位于右侧控制面板上。 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>关闭 Azure FXT Edge Filer 节点的电源

电源按钮可用于在测试期间以及将其添加到群集之前关闭系统。 但是，在 Azure FXT Edge Filer 节点作为群集的一部分使用后，应使用群集控制面板软件来关闭硬件。 请参阅[如何安全地关闭 Azure FXT Edge Filer 硬件](fxt-power-off.md)了解有关详细信息。 

## <a name="next-steps"></a>后续步骤

完成硬件布线后，打开每个节点的电源并通过设置 root 密码对其进行初始化。 
> [!div class="nextstepaction"]
> [设置初始密码](fxt-node-password.md)
