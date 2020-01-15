---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 32df17ffed400af80eadadbdeaafbaa1e3e1dbce
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941709"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 中提供 Red Hat Enterprise Linux （RHEL）映像
Azure 针对不同用例提供各种 RHEL 映像。

## <a name="list-of-rhel-images"></a>RHEL 映像列表
这是 Azure 中提供的 RHEL 映像的列表。 除非另有说明，否则所有映像均为 LVM 分区，并附加到常规 RHEL 存储库（不是 EUS，而不是 E4S）。 以下映像当前可供一般使用：

产品| SKU | 分区 | 提供 | 说明
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理 |
|             | 6.8      | RAW    | Linux 代理 |
|             | 6.9      | RAW    | Linux 代理 |
|             | 6.10     | RAW    | Linux 代理 |
|             | 7-RAW    | RAW    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7-LVM    | LVM    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7. x 映像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7.2      | RAW    | Linux 代理 |
|             | 7.3      | RAW    | Linux 代理 |
|             | 7.4      | RAW    | Linux 代理 | 默认情况下，在2019年4月之前附加到 EUS 存储库。
|             | 7.5      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，截至2019年6月。
|             | 7.6      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，可能为2019。
|             | 7.7      | LVM    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 8        | LVM    | Linux 代理 | RHEL 2.x 映像系列
|             | 8-gen2   | LVM    | Linux 代理 | Hyper-v 第2代-RHEL 8.x 版映像。
RHEL-SAP      | 7.4      | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.4。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.5      | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.5。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.6      | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.5。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.7      | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.5。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
RHEL-SAP-HANA | 6.7      | RAW    | Linux 代理 | RHEL 6.7 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.2      | LVM    | Linux 代理 | RHEL 7.2 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.3      | LVM    | Linux 代理 | RHEL 7.3 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
RHEL-SAP-APPS | 6.8      | RAW    | Linux 代理 | RHEL 6.8 for SAP Business Applications。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.3      | LVM    | Linux 代理 | RHEL 7.3 for SAP Business Applications。 过时，取而代之的是 RHEL-SAP 映像。
RHEL-HA       | 7.4      | LVM    | Linux 代理 | RHEL 7.4 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
|             | 7.5      | LVM    | Linux 代理 | RHEL 7.5 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
|             | 7.6      | LVM    | Linux 代理 | RHEL 7.6 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
RHEL-SAP-HA   | 7.4      | LVM    | Linux 代理 | RHEL 7.4 for SAP with HA 加载项。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 7.5      | LVM    | Linux 代理 | RHEL 7.5 for SAP with HA 加载项。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 7.6      | LVM    | Linux 代理 | RHEL 7.6 for SAP with HA 加载项。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
rhel-byos     |rhel-lvm74| LVM    | Linux 代理 | RHEL 7.4 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm75| LVM    | Linux 代理 | RHEL 7.5 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm76| LVM    | Linux 代理 | RHEL 7.6 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm77| LVM    | Linux 代理 | RHEL 7.7 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm8 | LVM    | Linux 代理 | RHEL 8 BYOS 映像（RHEL 次版本显示在映像版本值中），不会附加到任何更新源，而不会向 RHEL 高级版收费。

## <a name="next-steps"></a>后续步骤
* 详细了解[Azure 中的 Red Hat 映像](./redhat-images.md)。
* 详细了解[Red Hat 更新基础结构](./redhat-rhui.md)。
* 了解有关[RHEL BYOS 产品/服务](./byos.md)的详细信息。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
