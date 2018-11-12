---
title: 使用 Azure 虚拟 WAN 来与 Azure 建立站点到站点连接 | Microsoft Docs
description: 本教程介绍如何使用 Azure 虚拟 WAN 来与 Azure 建立站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: c99d3df23e0ba9733e8762fe8fc22a4c69d3bcfb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236850"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建站点到站点连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE（IKEv1 和 IKEv2）VPN 连接来与 Azure 中的资源建立连接。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)

> [!NOTE]
> 如果你有多个站点，则通常会使用[虚拟 WAN 合作伙伴](https://aka.ms/virtualwan)来创建此配置。 但是，如果你熟悉网络技术并能够熟练配置自己的 VPN 设备，则可以自行创建此配置。
>

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 WAN
> * 创建站点
> * 创建中心
> * 将中心连接到站点
> * 将 VNet 连接到中心
> * 下载并应用 VPN 设备配置
> * 查看虚拟 WAN
> * 查看资源运行状况
> * 监视连接

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="vnet"></a>1.创建虚拟网络

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2.创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://aka.ms/azurevirtualwanpreviewfeatures)并使用 Azure 帐户登录。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>3.创建站点

创建任意数目的与物理位置对应的站点。 例如，如果你在纽约、伦敦和洛杉矶各有一个分支机构，请创建三个独立的站点。 这些站点包含本地 VPN 设备终结点。 目前，只能为站点指定一个专用地址空间。

1. 单击已创建的 WAN。 在 WAN 页上的“WAN 体系结构”下，单击“VPN 站点”打开“VPN 站点”页。
2. 在“VPN 站点”页上，单击“+创建站点”。
3. 在“创建站点”页上填写以下字段：

  * **名称** - 本地站点的名称。
  * **公开 IP 地址** - 驻留在本地站点中的 VPN 设备的公共 IP 地址。
  * **专用地址空间** - 位于本地站点的 IP 地址空间。 发往此地址空间的流量将路由到本地站点。
  * **订阅** - 验证订阅。
  * **资源组** - 要使用的资源组。
  * **位置**。
4. 单击“显示高级”查看其他设置。 可以选择“BGP”以启用 BGP，这会在 Azure 中为此站点创建的所有连接上启用此功能。 还可以输入**设备信息**（可选字段）。 这有助于 Azure 团队更好地了解你的环境，以便将来添加更多的可用优化选项，或帮助你进行故障排除。
5. 单击“确认”。
6. 单击“确认”后，在“VPN 站点”页上查看状态。 该站点将从“正在预配”转为“已预配”。

## <a name="hub"></a>4.创建中心

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>5.将站点与中心相关联

中心通常应将关联到 VNet 所在的同一区域中的站点。

1. 在“VPN 站点”页上，选择要与中心关联的一个或多个站点，然后单击“+新建中心关联”。
2. 在“将站点与一个或多个中心关联”页上，从下拉列表中选择中心。 可以通过单击“+添加关联”将站点与其他中心关联。
3. 还可以在此处添加特定的 **PSK**，或使用默认值。
4. 单击“确认”。
5. 可在“VPN 站点”页上查看连接状态。

## <a name="vnet"></a>6.将 VNet 连接到中心

此步骤在中心与 VNet 之间创建对等互连。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。
2. 在虚拟网络连接页上，单击“+添加连接”。
3. 在“添加连接”页上填写以下字段：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建对等互连连接。

## <a name="device"></a>7.下载 VPN 配置

使用 VPN 设备配置来配置本地 VPN 设备。

1. 在虚拟 WAN 的页面上，单击“概述”。
2. 在“概述”页的顶部，单击“下载 VPN 配置”。 Azure 会在资源组“microsoft-network-[location]”中创建一个存储帐户，其中，location 是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 将配置应用到 VPN 设备。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此节提供以下信息：

    * 虚拟中心 VNet 的**地址空间**<br>示例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 已连接到中心的 VNet 的**地址空间**<br>示例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 虚拟中心 vpngateway 的 IP 地址。 由于 vpngateway 的每个连接由采用主动-主动配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，可以看到为每个站点指定了“Instance0”和“Instance1”。<br>示例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * Vpngateway 连接配置详细信息，例如 BGP、预共享密钥等。PSK 是自动生成的预共享密钥。 始终可以在“概述”页中为自定义 PSK 编辑连接。
  
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
* 虚拟 WAN 只能使用基于路由的 VPN 设备和设备说明。

## <a name="viewwan"></a>8.查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>9.查看资源运行状况

1. 导航到 WAN。
2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。

## <a name="connectmon"></a>10.监视连接

创建一个连接，用于监视 Azure VM 与远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="cleanup"></a>11.清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 WAN
> * 创建站点
> * 创建中心
> * 将中心连接到站点
> * 将 VNet 连接到中心
> * 下载并应用 VPN 设备配置
> * 查看虚拟 WAN
> * 查看资源运行状况
> * 监视连接

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
