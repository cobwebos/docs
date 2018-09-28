---
title: Azure 虚拟 WAN 合作伙伴位置 | Microsoft Docs
description: 本文包含 Azure 虚拟 WAN 的合作伙伴和中心位置的列表
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984987"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虚拟 WAN 合作伙伴和虚拟中心位置

本文提供有关虚拟 WAN 针对连接到虚拟中心所支持的区域以及首选合作伙伴的信息。

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可以手动完成此操作，也可以通过虚拟 WAN 首选合作伙伴来使用首选的提供商设备完成此操作。 使用首选的合作伙伴设备可以降低操作难度、简化连接和进行配置管理。 本地设备到虚拟中心的连接将自动建立。 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 每个区域只能有一个中心。

## <a name="regions"></a>区域

支持和可用的区域的列表如下所示：

|地缘政治区域 | Azure 区域|
|---|---|
|北美 | 美国东部、美国西部、美国东部 2 区、美国西部 2 区、美国中部、美国中南部、美国中北部、美国中西部、加拿大中部、加拿大东部 |
|南美洲 |巴西南部 |
| 欧洲 | 法国中部、法国南部、北欧、西欧、英国西部、英国南部 |
| 亚洲 | 东亚、东南亚 |
| 日本  | 日本西部、日本东部 |
| 澳大利亚 | 澳大利亚东南部、澳大利亚东部 | 
| 澳大利亚政府 | 澳大利亚中部、澳大利亚中部 2 |
| 印度 | 印度西部、印度中部、印度南部 |
| 韩国 | 韩国中部、韩国南部 | 

## <a name="automation-from-connectivity-partners"></a>连接合作伙伴提供的自动化

本部分介绍连接提供商提供的自动化的高级详细信息。

连接到 Azure 虚拟 WAN 的设备具有内置的自动化连接功能。 这通常在设备管理 UI（或同等位置）中设置，该 UI 设置 VPN 分支设备到 Azure 虚拟中心 VPN 终结点（VPN 网关）之间的连接和配置管理。

以下高级自动化在设备控制台/管理中心中设置：

* 设备访问 Azure 虚拟 WAN 资源组的相应权限
* 将分支设备上传到 Azure 虚拟 WAN
* 自动下载 Azure 连接信息 
* 本地分支设备的配置 

某些连接合作伙伴可以扩展自动化，包括创建 Azure 虚拟中心 VNet 和 VPN 网关。 若想了解有关自动化的详细信息，请参阅[配置自动化 - WAN 合作伙伴](virtual-wan-configure-automation-providers.md)。

## <a name="connectivity-through-preferred-partners"></a>通过首选合作伙伴实现连接

如果以下部分未列出你的分支设备合作伙伴，请联系分支设备提供商，并请他们通过向 azurevirtualwan@microsoft.com 发送电子邮件与我们联系。

可以访问以下链接，收集有关首选合作伙伴提供的服务的更多信息。 

|首选合作伙伴|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

有关如何自动连接到 Azure 虚拟 WAN 的详细信息，请参阅[虚拟 WAN 合作伙伴 - 如何实现自动化](virtual-wan-configure-automation-providers.md)。
