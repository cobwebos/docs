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
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605479"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 中提供的红帽企业 Linux （RHEL） 映像
Azure 为不同的用例提供各种 RHEL 映像。

> [!NOTE]
> 所有 RHEL 映像在 Azure 公共云和 Azure 政府云中都有可用。 它们在 Azure 中国云中不可用。

## <a name="list-of-rhel-images"></a>RHEL 图像列表
这是 Azure 中可用的 RHEL 映像的列表。 除非另有说明，否则所有图像均通过 LVM 分区并附加到常规 RHEL 存储库（不是 EUS，而不是 E4S）。 以下图像目前可供一般使用：

产品/服务| SKU | 分区 | 设置 | 说明
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理 |
|             | 6.8      | RAW    | Linux 代理 |
|             | 6.9      | RAW    | Linux 代理 |
|             | 6.10     | RAW    | Linux 代理 |
|             | 7-RAW    | RAW    | Linux 代理 | RHEL 7.x 图像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7-LVM    | LVM    | Linux 代理 | RHEL 7.x 图像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。 如果要查找要部署的标准 RHEL 映像，请使用这组映像和/或其第 2 代对应映像。
|             | 7lvm-gen2| LVM    | Linux 代理 | 第 2 代，RHEL 7.x 系列图像。 <br> 默认情况下附加到常规存储库（不是 EUS）。 如果要查找要部署的标准 RHEL 映像，请使用这组映像和/或其第 1 代对应映像。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 图像系列。 <br> 默认情况下附加到常规存储库（不是 EUS）。
|             | 7.2      | RAW    | Linux 代理 |
|             | 7.3      | RAW    | Linux 代理 |
|             | 7.4      | RAW    | Linux 代理 | 默认自 2019 年 4 月起附加到 EUS 存储库。
|             | 74代2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.5      | RAW    | Linux 代理 | 默认截至 2019 年 6 月连接到 EUS 存储库。
|             | 75 代2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.6      | RAW    | Linux 代理 | 默认截至 2019 年 5 月连接到 EUS 存储库。
|             | 76 代2  | RAW    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 7.7      | LVM    | Linux 代理 | 默认情况下附加到 EUS 存储库。
|             | 8-LVM    | LVM    | Linux 代理 | RHEL 8.x 图像系列。 附加到常规存储库。
|             | 8-lvm-gen2| LVM    | Linux 代理 | Hyper-V 第 2 代 - RHEL 8.x 图像系列。 附加到常规存储库。
|             | 8        | LVM    | Linux 代理 | RHEL 8.0 图像
|             | 8 代2   | LVM    | Linux 代理 | Hyper-V 第 2 代 - RHEL 8.0 图像。
|             | 8.1      | LVM    | Linux 代理 | RHEL 8.1 图像。 当前附加到常规存储库。
|             | 81x2   | LVM    | Linux 代理 | Hyper-V 第 2 代 - RHEL 8.1 图像。 当前附加到常规存储库。
RHEL-SAP      | 7.4      | LVM    | Linux 代理 | RHEL 7.4 适用于 SAP HANA 和业务应用。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 74sap-gen2| LVM    | Linux 代理 | RHEL 7.4 适用于 SAP HANA 和业务应用。 第 2 代图像。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 7.5       | LVM    | Linux 代理 | RHEL 7.5 适用于 SAP HANA 和业务应用。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 75sap-gen2| LVM    | Linux 代理 | RHEL 7.5 适用于 SAP HANA 和业务应用。 第 2 代图像。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 7.6       | LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.6。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 76sap-gen2| LVM    | Linux 代理 | 适用于 SAP HANA 和商业应用的 RHEL 7.6。 第 2 代图像。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
|             | 7.7       | LVM    | Linux 代理 | RHEL 7.7 适用于 SAP HANA 和业务应用。 连接到 E4S 存储库后，SAP 和 RHEL 将收取额外费用以及基本计算费用。
RHEL-SAP-HANA | 6.7       | RAW    | Linux 代理 | RHEL 6.7 用于 SAP HANA。 过时的，有利于RHEL-SAP图像。
|             | 7.2       | LVM    | Linux 代理 | RHEL 7.2 用于 SAP HANA。 过时的，有利于RHEL-SAP图像。
|             | 7.3       | LVM    | Linux 代理 | RHEL 7.3 用于 SAP HANA。 过时的，有利于RHEL-SAP图像。
RHEL-SAP-APPS | 6.8       | RAW    | Linux 代理 | 适用于 SAP 业务应用程序的 RHEL 6.8。 过时的，有利于RHEL-SAP图像。
|             | 7.3       | LVM    | Linux 代理 | 适用于 SAP 业务应用程序的 RHEL 7.3。 过时的，有利于RHEL-SAP图像。
雷尔-哈       | 7.4       | LVM    | Linux 代理 | RHEL 7.4 与 HA 附加组件。 将在基本计算费的基础上收取 HA 和 RHEL 的额外费用。
|             | 7.5       | LVM    | Linux 代理 | RHEL 7.5 与 HA 附加组件。 将在基本计算费的基础上收取 HA 和 RHEL 的额外费用。
|             | 7.6       | LVM    | Linux 代理 | RHEL 7.6 与 HA 附加组件。 将在基本计算费的基础上收取 HA 和 RHEL 的额外费用。
雷尔-萨普哈   | 7.4          | LVM    | Linux 代理 | RHEL 7.4 用于 SAP 与 HA 和更新服务。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 74萨帕-根2 | LVM    | Linux 代理 | RHEL 7.4 用于 SAP 与 HA 和更新服务。 第 2 代图像。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 7.5          | LVM    | Linux 代理 | RHEL 7.5 用于 SAP 与 HA 和更新服务。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 7.6          | LVM    | Linux 代理 | RHEL 7.6 用于 SAP 和更新服务。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 76萨帕-根2 | LVM    | Linux 代理 | RHEL 7.6 用于 SAP 和更新服务。 第 2 代图像。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 7.7          | LVM    | Linux 代理 | RHEL 7.7 用于 SAP 与 HA 和更新服务。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
|             | 77萨帕-根2 | LVM    | Linux 代理 | RHEL 7.7 用于 SAP 与 HA 和更新服务。 第 2 代图像。 附加到 E4S 存储库。 除了基本计算费用外，还将对 SAP 和 HA 存储库以及 RHEL 收取额外费用。
雷尔-比奥斯     |rhel-lvm74| LVM    | Linux 代理 | RHEL 7.4 BYOS 映像（未附加到任何更新源）不会收取 RHEL 溢价。
|             |rhel-lvm75| LVM    | Linux 代理 | RHEL 7.5 BYOS 映像（未附加到任何更新源）不会收取 RHEL 溢价。
|             |rhel-lvm76| LVM    | Linux 代理 | RHEL 7.6 BYOS 映像（未附加到任何更新源）不会收取 RHEL 溢价。
|             |rhel-lvm77| LVM    | Linux 代理 | RHEL 7.7 BYOS 映像（未附加到任何更新源）不会收取 RHEL 溢价。
|             |rhel-lvm8 | LVM    | Linux 代理 | RHEL 8 BYOS 映像（RHEL 次要版本显示在映像版本值中），不附加到任何更新源，不会收取 RHEL 溢价。

> [!NOTE]
> RHEL-SAP-HANA 产品被红帽视为生命周期终止。 现有部署将继续正常工作，但红帽建议客户从 RHEL-SAP-HANA 映像迁移到 RHEL-SAP-HA 映像，其中包括 SAP HANA 存储库以及 HA 加载项。 有关红帽 SAP 云产品的更多详细信息，[请点击此处](https://access.redhat.com/articles/3751271)。

## <a name="next-steps"></a>后续步骤
* 了解有关 Azure[中的红帽映像](./redhat-images.md)的更多详细信息。
* 了解有关[红帽更新基础结构](./redhat-rhui.md)的详细信息。
* 了解有关[RHEL BYOS 产品/](./byos.md)
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
