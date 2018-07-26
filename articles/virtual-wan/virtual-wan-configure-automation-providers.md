---
title: 配置 Azure 虚拟 WAN 自动化 - 适用于虚拟 WAN 合作伙伴| Microsoft Docs
description: 本文帮助软件定义的连接解决方案合作伙伴设置 Azure 虚拟 WAN 自动化。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: a1ff4364e394b3807cf767722ee934ae024399b0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114338"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>配置虚拟 WAN 自动化 - 适用于虚拟 WAN 合作伙伴（预览版）

本文将帮助你了解如何设置自动化环境以连接和配置 Azure 虚拟 WAN 的分支设备（客户本地 VPN 设备或 SDWAN）。 如果你是可通过 IPsec/IKEv2 容纳 VPN 连接的分支设备的提供商，那么本文对你有帮助。

软件定义的连接解决方案通常使用控制器或设备预配中心来管理其分支设备。 控制器可使用 Azure API 自动连接到 Azure 虚拟 WAN。 此类型的连接要求位于本地的 SDWAN 或 VPN 设备分配有一个面向外部的公共 IP 地址。

##  <a name="access"></a>访问控制

客户必须能够在设备 UI 中为虚拟 WAN 设置正确的访问控制。 建议使用 Azure 服务主体。 基于服务主体的访问为设备控制器提供正确的身份验证以上传分支信息。

##  <a name="site"></a>上传分支信息

设计用户体验以将分支（本地站点）信息上传到 Azure。 VPNSite 的 [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) 可用于在虚拟 WAN 中创建站点信息。 可提供所有分支 SDWAN/VPN 设备，或根据需要选择设备自定义。

##  <a name="hub"></a>中心和服务

将分支设备上传到 Azure 后，客户通常会在 Azure 门户中选择中心区域和/或服务，这会调用一组操作在中心内创建中心虚拟网络和 VPN 终结点。 VPN 网关是一个可扩展的网关，可根据带宽和连接需求进行适当调整。

## <a name="device"></a>设备配置

在此步骤中，未使用提供程序的客户将手动下载 Azure 配置并将其应用于本地 SDWAN/VPN 设备。 作为提供商，你应自动执行此步骤。 控制器可调用 GetVpnConfiguration REST API 来下载 Azure 配置，该配置通常类似于以下文件。

**配置说明**

  * 如果 Azure VNet 附加到虚拟中心，它们将显示为 ConnectedSubnets。
  * VPN 连接使用基于路由的配置和 IKEv2。

### <a name="understanding-the-device-configuration-file"></a>了解设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此部分提供以下信息：

    * 虚拟中心 VNet 的地址空间。<br>示例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 已连接到中心的 VNet 的地址空间。<br>示例：

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

## <a name="default"></a>默认策略

### <a name="initiator"></a>发起程序

以下部分列出了 Azure 作为隧道发起程序时支持的策略组合。

**阶段 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**阶段 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>响应方

以下部分列出了 Azure 作为隧道响应方时支持的策略组合。

**阶段 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**阶段 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>虚拟中心 vpngateway 策略与本地 SDWAN/VPN 设备或 SD-WAN 配置之间是否都需要匹配？

本地 SDWAN/VPN 设备或 SD-WAN 配置必须匹配或包含在 Azure IPsec/IKE 策略中指定的以下算法和参数。 SA 生存期为本地规范，无需匹配。

* IKE 加密算法
* IKE 完整性算法
* DH 组
* IPsec 加密算法
* IPsec 完整性算法
* PFS 组

## <a name="feedback"></a>预览版反馈

如能提供反馈，我们将不胜感激。 请向 <azurevirtualwan@microsoft.com> 发送电子邮件以报告问题，或提供有关虚拟 WAN 的反馈（正面或负面）。 请在主题行中添加你的公司名称（用“[ ]”括起来）。 若要报告问题，还请添加订阅 ID。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[关于 Azure 虚拟 WAN](virtual-wan-about.md) 和 [Azure 虚拟 WAN 常见问题解答](virtual-wan-faq.md)。
