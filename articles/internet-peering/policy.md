---
title: 微软对等策略
titleSuffix: Azure
description: 微软对等策略
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775220"
---
# <a name="peering-policy"></a>对等互连策略
Microsoft 对网络的一般要求如下说明。 它们适用于直接对等互连和 Exchange 对等互连请求。

## <a name="technical-requirements"></a>技术要求

* 完全冗余的网络，有足够的容量来交换流量，而不会造成拥塞。
* 对等体将具有可公开路由的自治系统编号 （ASN）。
* IPv4 和 IPv6 都支持，Microsoft 希望在每个对等位置建立这两种类型的会话。
* 不支持 MD5。
* **ASN 详细信息：**
    * Microsoft 管理 AS8075 以及以下 ASN：AS8068、AS8069、AS12076。 有关具有 AS8075 对等互连的完整 ASN 列表，请参阅 AS-SET MICROSOFT。
    * 与 Microsoft 同行的各方均同意在任何情况下都不接受 AS12076（快速路线）的路线，并应过滤掉所有对等方的 AS12076。
* **路由策略：**
    * 对等方将至少有一个公开可路由 /24。
    * 微软将覆盖收到的多退出歧视器 （MED）。
    * Microsoft 更喜欢从对等方接收 BGP 社区标记来指示路由发起。
    * 同行应将其路由注册在公共互联网路由注册表 （IRR） 数据库中，以便进行筛选，并将真诚地努力使此信息保持最新。
    * 我们建议对等体在与 Microsoft 的对等会话中设置最大前缀 1000 （IPv4） 和 100 （IPv6） 路由。
    * 除非事先明确约定，否则同行应宣布与 Microsoft 对等的所有位置的一致路由。
    * 通常，AS8075 的对等会话将通告所有 AS-MICROSOFT 路由。 AS8075 在非洲和亚洲的互连可能仅限于与各自区域相关的航线。
    * 任何一方都不会建立静态路线、最后手段的路线，或者将流量发送给另一方，用于未通过 BGP 宣布的路由。
    * 同行应遵守[MANRS](https://www.manrs.org/)的路由安全标准。

## <a name="operational-requirements"></a>业务要求
* 配备齐全的 24x7 网络运营中心 （NOC），能够协助解决所有技术和性能问题、安全违规、拒绝服务攻击或源自同行或其客户的任何其他滥用行为。
* 同行在[PeeringDB](https://www.peeringdb.com)上应拥有完整和最新的配置文件，包括来自公司域和电话号码的 24x7 NOC 电子邮件。 我们使用这些信息来验证同行的详细信息，如 NOC 信息、技术联系信息及其在对等设施的存在等。不接受个人雅虎、Gmail和热邮帐户。

## <a name="physical-connection-requirements"></a>物理连接要求
* 可在对等互连或交换对等互连中与 Microsoft 连接的位置列在[对等互连](https://www.peeringdb.com/net/694)中
* **Exchange 对等互连：**
    * 互连必须使用适当的 10Gbps 光学元件在单模光纤上。
    * 当峰值利用率超过 50% 时，对等体应升级其端口。
* **直接对等互连：**
    * 互连必须使用适当的 10Gbps 或 100Gbps 光纤在单模光纤上。
    * Microsoft 将仅与 ISP 或网络服务提供商建立直接对等互连。
    * 当峰值利用率超过 50% 时，同行应升级其端口，并在每个地铁中保持不同的容量，无论是在单个位置内，还是跨地铁中的多个位置。
    * 每个直接对等互连由两个连接组成，从位于对等器边缘的对等路由器连接到两个 Microsoft 边缘路由器。 Microsoft 需要跨这些连接进行双 BGP 会话。 对等体可以选择不在其末尾部署冗余设备。

## <a name="traffic-requirements"></a>流量要求
* 对等互连的对等体必须至少具有 200Mb 的流量和小于 2Gb 的流量。  对于流量超过 2Gb 的直接对等互连，应进行审查。
* 对于直接对等互连，从网络到 Microsoft 的流量必须满足低于最低要求。

    | 地域                      | 到微软的最低流量   |
    | :----------------------- |:-------------------------------|
    | 非洲                   | 500 Mbps                       |
    | 亚太地区（印度除外）      |   2 Gbps                       |
    | 亚太地区（仅限印度）        | 500 Mbps                       |
    | 欧洲                   |   2 Gbps                       |
    | 拉塔姆                    |   2 Gbps                       |
    | 中东              | 500 Mbps                       |
    | NA                       |   2 Gbps                       |

* **多样性：**
    * 在 NA、欧洲、亚太地区和 LATAM，如果可行，在至少三个地理位置不同的地点进行互连，并保持不同的容量，以便在每个地铁内实现交通故障转移。
    * 在非洲、中东和印度，在尽可能多的不同地点相互连接。 必须保持足够的多样化能力，以确保区域内的交通保持畅通。

## <a name="next-steps"></a>后续步骤

* 要了解设置与 Microsoft 的直接对等互连的步骤，请按照[直接对等演练进行](walkthrough-direct-all.md)。
* 要了解与 Microsoft 设置 Exchange 对等互连的步骤，请按照[Exchange 对等演练进行](walkthrough-exchange-all.md)。