---
title: Azure 虚拟 WAN 合作伙伴 | Microsoft Docs
description: 本文帮助合作伙伴设置 Azure 虚拟 WAN 自动化。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: cherylmc
ms.openlocfilehash: 6f95107960cf11ab15cb5a8294a5432498956a7a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775320"
---
# <a name="virtual-wan-partners"></a>虚拟 WAN 合作伙伴

本文介绍如何设置自动化环境以连接和配置 Azure 虚拟 WAN 的分支设备（客户本地 VPN 设备或 SDWAN CPE）。 如果你是可通过 IPsec/IKEv2 或 IPsec/IKEv1 容纳 VPN 连接的分支设备的提供商，那么本文对你有帮助。

分支设备（客户的本地 VPN 设备或 SDWAN CPE）通常使用需要预配的控制器/设备仪表板。 SD-WAN 解决方案管理员通常可以使用管理控制台来提前预配一个设备，然后再将该设备加入到网络中。 这个支持 VPN 的设备从控制器获取其控制平面逻辑。 VPN 设备或 SD-WAN 控制器可使用 Azure API 自动连接到 Azure 虚拟 WAN。 此类型的连接要求本地设备分配有一个面向外部的公共 IP 地址。

## <a name ="before"></a>开始自动化操作之前

* 验证设备是否支持 IPsec IKEv1/IKEv2。 请参阅[默认策略](#default)。
* 查看用于自动连接到 Azure 虚拟 WAN 的[REST api](#additional) 。
* 测试 Azure 虚拟 WAN 的门户体验。
* 然后，确定连接步骤中的哪部分需要自动化。 建议至少自动化以下部分：

  * 访问控制
  * 将分支设备信息上传到 Azure 虚拟 WAN
  * 下载 Azure 配置，并设置从分支设备到 Azure 虚拟 WAN 的连接

### <a name ="additional"></a>附加信息

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs)以自动创建虚拟中心
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways)为虚拟广域网自动执行 Azure VPN 网关
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections)将 VPNSite 连接到 Azure VPN 集线器
* [默认 IPsec 策略](#default)

## <a name ="ae"></a>客户体验

了解客户在结合 Azure 虚拟 WAN 进行操作时的预期体验。

  1. 通常情况下，虚拟 WAN 用户一开始会创建虚拟 WAN 资源。
  2. 该用户会为本地系统（分支控制器或 VPN 设备预配软件）设置基于服务主体的资源组访问权限，以便将分支信息写入到 Azure 虚拟 WAN 中。
  3. 用户可能会在此时决定登录 UI 并设备服务主体凭据。 该操作完成以后，控制器应该就能够使用你将要提供的自动化设置来上传分支信息。 此操作在 Azure 端的手动等效项是“创建站点”。
  4. 一旦 Azure 中提供了站点（分支设备）信息，用户就会将该站点连接到中心。 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 中心是区域中网络的核心。 每个 Azure 区域只能有一个中心，其中的 VPN 终结点 (vpngateway) 在此过程中创建。 VPN 网关是一个可扩展的网关，可根据带宽和连接需求进行适当调整。 可以在分支设备控制器仪表板中选择自动创建虚拟中心和 vpngateway。
  5. 将虚拟中心关联到站点以后，将会生成一个配置文件，供用户手动下载。 可以在其中放置自动化设置，实现无缝的用户体验。 可以在 UI 中设置自动化并提供尽量减少点击的体验，这样就不需用户手动下载和配置分支设备，减少典型的连接问题，例如共享密钥不匹配问题、IPSec 参数不匹配问题、配置文件可读性问题，等等。
  6. 解决方案中的此步骤结束时，用户就可以在分支设备和虚拟中心之间进行无缝的站点到站点连接。 也可设置跨其他中心的其他连接。 每个连接都是主动-主动隧道。 客户可以选择针对隧道的每个链路使用不同的 ISP。
  7. 请考虑在 CPE 管理界面中提供故障排除和监视功能。 典型方案包括 "客户无法访问 Azure 资源，因为出现了 CPE 问题"、"在 CPE 端显示 IPsec 参数" 等。

## <a name ="understand"></a>自动化详细信息

###  <a name="access"></a>访问控制

客户必须能够在设备 UI 中为虚拟 WAN 设置正确的访问控制。 建议使用 Azure 服务主体。 基于服务主体的访问为设备控制器提供正确的身份验证以上传分支信息。 有关详细信息，请参阅[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 虽然此功能不在 Azure 虚拟 WAN 套餐之中，但我们仍将在 Azure 中设置访问权限所需采取的典型步骤列在下面。相关详细信息随后会输入到设备管理仪表板中

* 为本地设备控制器创建 Azure Active Directory 应用程序。
* 获取应用程序 ID 和身份验证密钥
* 获取租户 ID
* 将应用程序分配到“参与者”角色

###  <a name="branch"></a>上传分支设备信息

你应设计用户体验，将分支（本地站点）信息上载到 Azure。 可以使用 VPNSite 的[REST api](https://docs.microsoft.com/rest/api/virtualwan/vpnsites)在虚拟 WAN 中创建站点信息。 可提供所有分支 SDWAN/VPN 设备，或根据需要选择设备自定义。

### <a name="device"></a>设备配置下载和连接

此步骤包括下载 Azure 配置，并设置从分支设备到 Azure 虚拟 WAN 的连接。 在此步骤中，未使用提供程序的客户将手动下载 Azure 配置并将其应用于本地 SDWAN/VPN 设备。 作为提供商，你应自动执行此步骤。 有关其他信息，请查看下载[REST api](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) 。 设备控制器可以调用 "GetVpnConfiguration" REST API 下载 Azure 配置。

**配置说明**

  * 如果 Azure VNet 附加到虚拟中心，它们将显示为 ConnectedSubnets。
  * VPN 连接使用基于路由的配置，同时支持 IKEv1 和 IKEv2 协议。

## <a name="devicefile"></a>设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此部分提供以下信息：

    * 虚拟中心 VNet 的地址空间。<br>示例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 已连接到中心的 VNet 的地址空间。<br>示例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 虚拟中心 vpngateway 的 IP 地址。 由于 vpngateway 的每个连接由采用主动-主动配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，可以看到为每个站点指定了“Instance0”和“Instance1”。<br>示例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway 连接配置详细信息**，例如 BGP、预共享密钥等等。PSK 是自动为您生成的预共享密钥。 始终可以在“概述”页中为自定义 PSK 编辑连接。
  
**设备配置文件示例**

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
                  "10.3.0.0/16"
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

## <a name="default"></a>连接详细信息

本地 SDWAN/VPN 设备或 SD-WAN 配置必须匹配或包含在 Azure IPsec/IKE 策略中指定的以下算法和参数。

* IKE 加密算法
* IKE 完整性算法
* DH 组
* IPsec 加密算法
* IPsec 完整性算法
* PFS 组

### <a name="default"></a>IPsec 连接的默认策略

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom"></a>IPsec 连接的自定义策略

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[关于 Azure 虚拟 WAN](virtual-wan-about.md) 和 [Azure 虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

有关其他信息，请发送电子邮件至 <azurevirtualwan@microsoft.com>。 请在主题行中添加你的公司名称（用“[ ]”括起来）。
