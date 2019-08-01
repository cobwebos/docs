---
title: 监视 Azure 上的 SAP HANA（大型实例）| Microsoft Docs
description: 监视 Azure 上的 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de7066004c4baa6e3086f2909d9d5150b50d8e41
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570551"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>如何监视 Azure 上的 SAP HANA（大型实例）

Azure 上的 SAP HANA（大型实例）与其他任何 IaaS 部署一样，都需要监视 OS 与应用程序在执行哪些操作，以及这些应用程序对以下资源的消耗情况：

- CPU
- 内存
- 网络带宽
- 磁盘空间

对于 Azure 虚拟机，你需要确定上述资源类是否足够，或者它们是否已耗尽。 下面提供了有关每个不同的类的更多详细信息：

**CPU 资源消耗:** 对针对 HANA 的特定工作负荷定义 SAP 所需的比率, 以确保有足够的 CPU 资源可用于处理存储在内存中的数据。 不过，在某些情况下，HANA 可能会由于缺失索引或类似问题，在执行查询时消耗大量 CPU 资源。 这意味着，应该监视 HANA 大型实例单元消耗的 CPU 资源，以及特定 HANA 服务消耗的 CPU 资源。

**内存占用:** 很重要的一点是, 从 HANA 内部监视, 以及在该单元上的 HANA 外部监视。 在 HANA 内部，应该监视数据对 HANA 分配的内存的消耗情况，使之不会超出 SAP 规定的消耗量指导原则。 此外，还需要在大型实例级别监视内存消耗，确保其他已安装的非 HANA 软件不会消耗过多的内存，从而与 HANA 争用内存。

**网络带宽:** Azure VNet 网关的带宽限制为移动到 Azure VNet 中的数据, 因此, 监视 VNet 中的所有 Azure Vm 接收的数据有助于确定你所选择的 Azure 网关 SKU 的限制。 在 HANA 大型实例单元上，监视传入和传出的网络流量以及跟踪不同时间处理的数据量确实很有作用。

**磁盘空间：** 磁盘空间消耗通常会随着时间的推移而增加。 最常见的原因包括：数据量增加、执行事务日志备份、存储跟踪文件，以及执行存储快照。 因此，必须监视磁盘空间用量，管理与 HANA 大型实例单元相关的磁盘空间。

对于 HANA 大型实例类型 II SKU，服务器附带预载的系统诊断工具。 可以利用这些诊断工具执行系统健康状况检查。 运行以下命令，在 /var/log/health_check 生成健康状况检查日志文件。
```
/opt/sgi/health_check/microsoft_tdi.sh
```
同 Microsoft 支持团队一起排除故障时，还可能需要使用这些诊断工具提供日志文件。 可以使用以下命令压缩文件。
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**后续步骤**

- 参阅[如何监视 Azure 上的 SAP HANA（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)。
