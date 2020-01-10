---
title: Microsoft 对等互连策略
titleSuffix: Azure
description: Microsoft 对等互连策略
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775220"
---
# <a name="peering-policy"></a>对等策略
Microsoft 对网络的一般要求在以下各节中进行了介绍。 它们适用于直接对等互连请求和 Exchange 对等互连请求。

## <a name="technical-requirements"></a>技术要求

* 具有足够容量的完全冗余网络，无需拥塞即可交换通信。
* 对等端将具有可路由的自治系统编号（ASN）。
* IPv4 和 IPv6 均受支持，Microsoft 希望在每个对等位置建立两种类型的会话。
* MD5 不受支持。
* **ASN 详细信息：**
    * Microsoft 管理 AS8075 以及以下 Asn： AS8068、AS8069、AS12076。 有关 AS8075 对等互连的 Asn 的完整列表，请参阅 MICROSOFT。
    * 与 Microsoft 的所有参与方都同意在任何情况下都不接受来自 AS12076 （Express Route）的路由，并且应筛选出所有对等方的 AS12076。
* **路由策略：**
    * 对等机将至少有一个公开可路由的/24。
    * Microsoft 将覆盖收到的多出口鉴别器（MED-V）。
    * Microsoft 首选接收来自对等方的 BGP 社区标记，以指示路由源。
    * 对等端应在公共 Internet 路由注册表（IRR）数据库中注册其路由，以便进行筛选，并将此信息保持在最新状态，从而使其成为一项不错的努力。
    * 建议对等互连在与 Microsoft 的对等互连会话上设置最大前缀1000（IPv4）和100（IPv6）路由。
    * 除非事先明确达成共识，否则对等方应在与 Microsoft 对等的所有位置公布一致的路由。
    * 通常，具有 AS8075 的对等互连会话将播发所有 AS-MICROSOFT 路由。 非洲和亚洲的 AS8075 互连可能会限制为与相应区域相关的路由。
    * 任何一方都不会建立静态路由、最后一次使用的路由，或以其他方式将流量发送到未通过 BGP 公布的路由。
    * 对等方应遵守路由安全的[MANRS](https://www.manrs.org/)行业标准。

## <a name="operational-requirements"></a>操作要求
* 一项完全配备的24x7 网络运营中心（NOC），能够帮助解决所有的技术和性能问题、安全违规、拒绝服务攻击或源自对等方或其客户的任何其他滥用。
* 对等端应在[PeeringDB](https://www.peeringdb.com)上具有完整的和最新的配置文件，包括来自企业域和电话号码的 24x7 NOC 电子邮件。 我们使用此信息来验证对等方的详细信息，例如 NOC 信息、技术联系人信息，以及它们在对等互连设施上的存在性等。不接受个人 Yahoo、Gmail 和 hotmail 帐户。

## <a name="physical-connection-requirements"></a>物理连接要求
* [PeeringDB](https://www.peeringdb.com/net/694)中列出了可以连接到 Microsoft 以实现直接对等互连或 Exchange 对等互连的位置。
* **Exchange 对等互连：**
    * 使用适当的10Gbps 光学器件，互连必须通过单模式纤程。
    * 当高峰使用量超过50% 时，对等机应升级其端口。
* **直接对等互连：**
    * 必须使用合适的10Gbps 或100Gbps 光纤将互连置于单模式光纤上。
    * Microsoft 将仅与 ISP 或网络服务提供商建立直接对等互连。
    * 当高峰使用量超过50% 时，对等方应升级其端口，并在每个大都市范围内的每个大都市内维护不同的容量。
    * 每个直接对等互连都包含两个从对等机的路由器中的两个 Microsoft 边缘路由器的连接。 Microsoft 要求跨这些连接进行双重 BGP 会话。 对等方可以选择不在其一端部署冗余设备。

## <a name="traffic-requirements"></a>流量要求
* 跨 Exchange 对等互连的对等机必须至少具有200Mb 的流量并小于2Gb。  应查看超出2Gb 直接对等互连的流量。
* 对于直接对等互连，从网络到 Microsoft 的流量必须满足以下最低要求。

    | 地域                      | 向 Microsoft 发送的最小流量   |
    | :----------------------- |:-------------------------------|
    | 非洲                   | 500 Mbps                       |
    | APAC （印度除外）      |   2 Gbps                       |
    | APAC （仅限印度）        | 500 Mbps                       |
    | 欧洲                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | 中东              | 500 Mbps                       |
    | 不可用                       |   2 Gbps                       |

* **多样性**
    * 在 NA、欧洲、APAC 和 LATAM 中，在至少三个地理位置相同的位置进行互连，并维护不同的容量，以允许流量在每地铁范围内进行故障转移。
    * 在非洲、中东和印度，尽可能多地在尽可能多的地方进行互连。 必须维护足够大的容量，以确保流量仍在区域中。

## <a name="next-steps"></a>后续步骤

* 若要了解有关设置与 Microsoft 的直接对等互连的步骤，请遵循[直接对等演练](walkthrough-direct-all.md)。
* 若要了解有关与 Microsoft 建立 Exchange 对等互连的步骤，请遵循[exchange 对等互连演练](walkthrough-exchange-all.md)。