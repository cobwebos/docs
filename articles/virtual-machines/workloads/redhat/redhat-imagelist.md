---
title: Azure 中提供 Red Hat Enterprise Linux 映像
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
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: f4ebf433237193c0db7859b029bb224c72c92eae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291015"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux (RHEL) Azure 中提供的映像
Azure 针对不同用例提供各种 RHEL 映像。

> [!NOTE]
> 所有 RHEL 映像都在 Azure 公共和 Azure 政府云中提供。 它们在 Azure 中国云中不可用。

## <a name="list-of-rhel-images"></a>RHEL 映像列表
这是 Azure 中提供的 RHEL 映像的列表。 除非另有说明，否则所有映像都是 LVM 分区并附加到常规 RHEL 存储库， (不 EUS，而不是 E4S) 。 以下映像当前可供一般使用：

> [!NOTE]
> 不会再生成原始映像以支持 LVM 分区映像。 LVM 与较旧的原始 (非 LVM) 分区方案相比，具有多项优势，包括更灵活的分区调整大小选项。

产品/服务| SKU | 分区 | 设置 | 注意
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理 |
|             | 6.8      | RAW    | Linux 代理 |
|             | 6.9      | RAW    | Linux 代理 |
|             | 6.10     | RAW    | Linux 代理 |
|             | 7-RAW    | RAW    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下，附加到常规存储库 (不 EUS) 。
|             | 7-LVM    | LVM    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下，附加到常规存储库 (不 EUS) 。 如果你正在寻找要部署的标准 RHEL 映像，请使用此映像集和/或其第2代副本。
|             | 7lvm-gen2| LVM    | Linux 代理 | 第2代，RHEL 7. x 映像系列。 <br> 默认情况下，附加到常规存储库 (不 EUS) 。 如果你正在寻找要部署的标准 RHEL 映像，请使用此映像集和/或其第1代映像。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7. x 映像系列。 <br> 默认情况下，附加到常规存储库 (不 EUS) 。
|             | 7.2      | RAW    | Linux 代理 |
|             | 7.3      | RAW    | Linux 代理 |
|             | 7.4      | RAW    | Linux 代理 | 默认情况下，在2019年4月之前附加到 EUS 存储库。
|             | 74-gen2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.5      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，截至2019年6月。
|             | 75-gen2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.6      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，可能为2019。
|             | 76-gen2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.7      | LVM    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 77-gen2  | LVM    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.8      | LVM    | Linux 代理 |  (EUS 无法用于 RHEL 7.8) 连接到常规存储库
|             | 78-gen2  | LVM    | Linux 代理 |  (EUS 无法用于 RHEL 7.8) 连接到常规存储库
|             | 8-LVM    | LVM    | Linux 代理 | RHEL 2.x 映像系列。 已附加到常规存储库。
|             | 8-lvm-gen2| LVM    | Linux 代理 | Hyper-v 第2代-RHEL 8.x 版映像。 已附加到常规存储库。
|             | 8        | LVM    | Linux 代理 | RHEL 8.0 映像。
|             | 8-gen2   | LVM    | Linux 代理 | Hyper-v 第2代-RHEL 8.0 映像。
|             | 8.1      | LVM    | Linux 代理 | RHEL 8.2 映像。 当前已附加到常规存储库。
|             | 81gen2   | LVM    | Linux 代理 | Hyper-v 第2代-RHEL 8.1 映像。 当前已附加到常规存储库。
|             | 8.1-ci   | LVM    | Linux 代理 | RHEL 8.1 映像，使用 cloud init 作为预配代理。 当前已附加到常规存储库。
|             | 81-ci-gen2| LVM    | Linux 代理 | Hyper-v 第2代-使用 cloud init 作为预配代理的 RHEL 8.1 映像。 当前已附加到常规存储库。
|             | 8.2      | LVM    | Linux 代理 | RHEL 8.2 映像。 当前已附加到常规存储库。
|             | 82gen2   | LVM    | Linux 代理 | Hyper-v 第2代-RHEL 8.1 映像。 当前已附加到常规存储库。
RHEL-SAP      | 7.4      | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.4。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 74sap-gen2| LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.4。 第2代映像。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.5       | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.5。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 75sap-gen2| LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.5。 第2代映像。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.6       | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.6。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 76sap-gen2| LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.6。 第2代映像。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
|             | 7.7       | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.7。 附加到 E4S 存储库，将为 SAP 和 RHEL 提供高级版，以及基础计算费用。
RHEL-SAP-HANA | 6.7       | RAW    | Linux 代理 | RHEL 6.7 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.2       | LVM    | Linux 代理 | RHEL 7.2 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.3       | LVM    | Linux 代理 | RHEL 7.3 for SAP HANA。 过时，取而代之的是 RHEL-SAP 映像。
RHEL-SAP-APPS | 6.8       | RAW    | Linux 代理 | RHEL 6.8 for SAP Business Applications。 过时，取而代之的是 RHEL-SAP 映像。
|             | 7.3       | LVM    | Linux 代理 | RHEL 7.3 for SAP Business Applications。 过时，取而代之的是 RHEL-SAP 映像。
RHEL-HA       | 7.4       | LVM    | Linux 代理 | RHEL 7.4 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
|             | 7.5       | LVM    | Linux 代理 | RHEL 7.5 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
|             | 7.6       | LVM    | Linux 代理 | RHEL 7.6 with HA 加载项。 将根据基础计算费用，对高可用性和 RHEL 的高级版进行收费。
RHEL-SAP-HA   | 7.4          | LVM    | Linux 代理 | RHEL 7.4，适用于具有 HA 和更新服务的 SAP。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 74sapha-gen2 | LVM    | Linux 代理 | RHEL 7.4，适用于具有 HA 和更新服务的 SAP。 第2代映像。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 7.5          | LVM    | Linux 代理 | RHEL 7.5，适用于具有 HA 和更新服务的 SAP。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 7.6          | LVM    | Linux 代理 | RHEL 7.6，适用于具有 HA 和更新服务的 SAP。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 76sapha-gen2 | LVM    | Linux 代理 | RHEL 7.6，适用于具有 HA 和更新服务的 SAP。 第2代映像。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 7.7          | LVM    | Linux 代理 | RHEL 7.7，适用于具有 HA 和更新服务的 SAP。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
|             | 77sapha-gen2 | LVM    | Linux 代理 | RHEL 7.7，适用于具有 HA 和更新服务的 SAP。 第2代映像。 已附加到 E4S 存储库。 将根据基础计算费用，为 SAP 和 HA 存储库以及 RHEL 支付高级版。
rhel-byos     |rhel-lvm74| LVM    | Linux 代理 | RHEL 7.4 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm75| LVM    | Linux 代理 | RHEL 7.5 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm76| LVM    | Linux 代理 | RHEL 7.6 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm76-gen2| LVM    | Linux 代理 | RHEL 7.6 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。
|             |rhel-lvm77| LVM    | Linux 代理 | RHEL 7.7 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm77-gen2| LVM    | Linux 代理 | RHEL 7.7 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。
|             |rhel-lvm78| LVM    | Linux 代理 | RHEL 7.8 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm78-gen2| LVM    | Linux 代理 | RHEL 7.8 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。
|             |rhel-lvm8 | LVM    | Linux 代理 | RHEL 8.0 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm8-gen2 | LVM    | Linux 代理 | RHEL 8.0 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。
|             |rhel-lvm81 | LVM    | Linux 代理 | RHEL 8.1 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm81-gen2 | LVM    | Linux 代理 | RHEL 8.1 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。
|             |rhel-lvm82 | LVM    | Linux 代理 | RHEL 8.2 BYOS 映像不会附加到任何更新源，也不会向 RHEL 高级版收费。
|             |rhel-lvm82-gen2 | LVM    | Linux 代理 | RHEL 8.2 第2代 BYOS 映像不会附加到任何更新源，而不会向 RHEL 高级版收费。

> [!NOTE]
> RHEL-SAP-HANA 产品产品的使用情况被视为 Red Hat 的生命周期。 现有部署将继续正常运行，但 Red Hat 建议客户从 RHEL-SAP HANA 映像迁移到 RHEL-SAP-HA 映像，其中包括 SAP HANA 存储库以及 HA 加载项。 [此处](https://access.redhat.com/articles/3751271)提供了有关 Red HAT 的 SAP 云产品/服务的更多详细信息。

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure 中的 Red Hat 映像](./redhat-images.md)。
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)。
* 了解有关 [RHEL BYOS 产品/服务](./byos.md)的详细信息。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
