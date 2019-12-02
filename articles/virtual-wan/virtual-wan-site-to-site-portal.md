---
title: Azure 虚拟 WAN：创建站点到站点连接
description: 本教程介绍如何使用 Azure 虚拟 WAN 来与 Azure 建立站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e17205af1ede845ea77b04f6f2b4c6babf3bc450
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482147"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建站点到站点连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE（IKEv1 和 IKEv2）VPN 连接来与 Azure 中的资源建立连接。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟 WAN
> * 创建中心
> * 创建站点
> * 将站点连接到中心
> * 将 VPN 站点连接到中心
> * 将 VNet 连接到中心
> * 下载配置文件
> * 查看虚拟 WAN

> [!NOTE]
> 如果你有多个站点，则通常会使用[虚拟 WAN 合作伙伴](https://aka.ms/virtualwan)来创建此配置。 但是，如果你熟悉网络技术并能够熟练配置自己的 VPN 设备，则可以自行创建此配置。
>

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="openvwan"></a>创建虚拟 WAN

从浏览器导航到 Azure 门户并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”  。 在搜索框中键入“虚拟 WAN”  ，然后选择 Enter。
2. 从结果中选择“虚拟 WAN”  。 在“虚拟 WAN”页上，单击“创建”以打开“创建 WAN”页  。
3. 在“创建 WAN”  页的“基本”  选项卡上，填写以下字段：

   ![虚拟 WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型：** 基本或标准。 如果创建基本 WAN，则只能创建基本中心。 基本中心仅支持 VPN 站点到站点连接。
4. 填写完字段后，单击“审阅 + 创建”  。
5. 验证通过后，选择“创建”  以创建虚拟 WAN。

## <a name="hub"></a>创建中心

中心是一种虚拟网络，可包含适用于站点到站点、ExpressRoute 或点到站点功能的网关。 创建中心后，即使你没有附加任何站点，也会对该中心收取费用。 在虚拟中心创建站点到站点 VPN 网关需要 30 分钟时间。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>创建站点

现在，你可以创建与物理位置对应的站点。 创建任意数目的与物理位置对应的站点。 例如，如果你在纽约、伦敦和洛杉矶各有一个分支机构，请创建三个独立的站点。 这些站点包含本地 VPN 设备终结点。 在虚拟 WAN 中，每个虚拟中心最多可以创建 1000 个站点。 如果有多个中心，则可以为每个中心创建 1000 个站点。 如果你有虚拟 WAN 合作伙伴（插入链接）CPE 设备，请咨询这些设备以了解其对 Azure 的自动化。 通常，自动化意味着只需执行简单的单击操作即可将大规模分支信息导出到 Azure 中，并设置从 CPE 到 Azure 虚拟 WAN VPN 网关的连接（此处是从 Azure 到 CPE 合作伙伴的自动化指南链接）。

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>将 VPN 站点连接到中心

此步骤将 VPN 站点连接到中心。

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>将 VNet 连接到中心

此步骤在中心与 VNet 之间创建互连。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。 
2. 在虚拟网络连接页上，单击“+添加连接”。 
3. 在“添加连接”页上填写以下字段  ：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”  以创建虚拟网络连接。

## <a name="device"></a>下载 VPN 配置

使用 VPN 设备配置来配置本地 VPN 设备。

1. 在虚拟 WAN 的页面上，单击“概述”。 
2. 在“中心 ->VPNSite”  页的顶部，单击“下载 VPN 配置”  。Azure 会在资源组“microsoft-network-[location]”中创建一个存储帐户，其中，location 是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 将配置应用到本地 VPN 设备。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此部分提供以下设置的信息：

    * 虚拟中心 VNet 的**地址空间**<br>示例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 已连接到中心的 VNet 的**地址空间**<br>示例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 虚拟中心 vpngateway 的 IP 地址  。 由于 vpngateway 的每个连接由采用主动 - 主动配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，可以看到为每个站点指定了“Instance0”和“Instance1”。<br>示例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * Vpngateway 连接配置详细信息，例如 BGP、预共享密钥等  。PSK 是自动生成的预共享密钥。 始终可以在“概述”页中为自定义 PSK 编辑连接。
  
### <a name="example-device-configuration-file"></a>示例设备配置文件

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>配置 VPN 设备

>[!NOTE]
> 如果正在使用虚拟 WAN 合作伙伴解决方案，则会自动进行 VPN 设备配置。 设备控制器将从 Azure 获取配置文件并应用于设备以设置与 Azure 的连接。 这意味着，无需知道如何手动配置 VPN 设备。
>

如需有关如何配置设备的说明，可以使用 [VPN 设备配置脚本页](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的说明，并注意以下事项：

* VPN 设备页上的说明不是针对虚拟 WAN 编写的，但你可以使用配置文件中的虚拟 WAN 值来手动配置 VPN 设备。 
* 适用于 VPN 网关的可下载设备配置脚本并不适用于虚拟 WAN，因为配置不同。
* 新的虚拟 WAN 可以同时支持 IKEv1 和 IKEv2。
* 虚拟 WAN 可以使用基于策略和基于路由的 VPN 设备和设备说明。

## <a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”  页上，地图中的每个点表示一个中心。 将鼠标悬停在任意点上可查看中心运行状况摘要、连接状态以及传入和传出的字节数。
3. 在“中心和连接”部分中，可以查看中心状态、VPN 站点等。可以单击特定的中心名称并导航到 VPN 站点以获取更多详细信息。

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
