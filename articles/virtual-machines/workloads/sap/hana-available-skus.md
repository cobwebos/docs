---
title: Azure SAP HANA（大型实例）的 SKU | Microsoft Docs
description: Azure SAP HANA（大型实例）的 SKU。
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
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 043ecdcb83d2ee39e3373f029c00c6237f87601c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709712"
---
# <a name="available-skus-for-hli"></a>对 HLI 可用的 SKU

多个 Azure 区域发布了多种配置的 Azure 上的 SAP HANA（大型实例）服务，其中包括美国西部和美国东部、澳大利亚东部、澳大利亚东南部、西欧、北欧、日本东部和日本西部。

[经 SAP HANA 认证的 HANA 大型实例 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 列表如下：

| SAP 解决方案 | CPU | 内存 | 存储 | 可用性 |
| --- | --- | --- | --- | --- |
| 已针对 OLAP 进行了优化：SAP BW、BW/4HANA<br /> 或 SAP HANA，适用于一般 OLAP 工作负荷 | Azure 上的 SAP HANA S72<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3<br /> 36 CPU 内核和 72 CPU 线程 |  768 GB |  3 TB | 不再提供 |
| --- | Azure 上的 SAP HANA S144<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3<br /> 72 CPU 内核和 144 CPU 线程 |  1.5 TB |  6 TB | 不再提供 |
| --- | Azure 上的 SAP HANA S192<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4<br /> 96 CPU 内核和 192 CPU 线程 |  2.0 TB |  8 TB | 可用 |
| --- | Azure 上的 SAP HANA S384<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  4.0 TB |  16 TB | 可用 |
| 已针对 OLTP 进行了优化：SAP Business Suite<br /> SAP Business Suite (OLTP)，<br /> 适用于一般 OLTP | Azure 上的 SAP HANA S72m<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3<br /> 36 CPU 内核和 72 CPU 线程 |  1.5 TB |  6 TB | 不再提供 |
|---| Azure 上的 SAP HANA S144m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3<br /> 72 CPU 内核和 144 CPU 线程 |  3.0 TB |  12 TB | 不再提供 |
|---| Azure 上的 SAP HANA S192m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4<br /> 96 CPU 内核和 192 CPU 线程  |  4.0 TB |  16 TB | 可用 |
|---| Azure 上的 SAP HANA S384m<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  6.0 TB |  18 TB | 可用 |
|---| Azure 上的 SAP HANA S384xm<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  8.0 TB |  22 TB |  可用 |
|---| Azure 上的 SAP HANA S576m<br /> – 12 x Intel® Xeon® 处理器 E7-8890 v4<br /> 288 CPU 内核和 576 CPU 线程 |  12.0 TB |  28 TB | 可用 |
|---| Azure 上的 SAP HANA S768m<br /> – 16 x Intel® Xeon® 处理器 E7-8890 v4<br /> 384 CPU 内核和 768 CPU 线程 |  16.0 TB |  36 TB | 可用 |
|---| Azure 上的 SAP HANA S960m<br /> – 20 x Intel® Xeon® 处理器 E7-8890 v4<br /> 480 CPU 内核和 960 CPU 线程 |  20.0 TB |  46 TB | 可用 |


在 SAP HANA TDIv5 下，SAP 允许特定于客户的规模调整和特定于客户的项目，这可能导致出现未经认证的服务器配置：

- [经 SAP HANA 认证的设备](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

在许多情况下，这些特定于客户的服务器配置比经 SAP 认证的服务器单元承载更多内存。 使用 SAP 时，客户可获得 SAP 支持并对其特定于客户的规模调整服务器配置进行认证。 Azure 提供了以下 HANA 大型实例标准 SKU，Microsoft 提供了此类 TDIv5 特定于客户的规模调整项目的价目表。

| SKU|CPU | 内存 | 存储 | 可用性 |
| ---| --- | --- | --- | --- |
| S96 | Azure 上的 SAP HANA S96<br /> \- 2 x Intel® Xeon® 处理器 E7-8890 v4<br /> 48 CPU 内核和 96 CPU 线程 |  768 GB |  3 TB | 可用 |


| 可在内存中 <br /> 扩展的原始 SKU | CPU | 内存 | 存储 | 可用性 |
| --- | --- | --- | --- | --- |
| S192m 可扩展为 | Azure 上的 SAP HANA S192xm<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4<br /> 96 CPU 内核和 192 CPU 线程 |  6.0 TB |  16 TB | 可用 |
| S384xm 可扩展为 | Azure 上的 SAP HANA S384xxm<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  12.0 TB |  28 TB | 可用 |
| S576m 可扩展为 | Azure 上的 SAP HANA S576xm<br /> – 12 x Intel® Xeon® 处理器 E7-8890 v4<br /> 288 CPU 内核和 576 CPU 线程 |  18.0 TB |  41 TB | 可用 |
| S768m 可扩展为 | Azure 上的 SAP HANA S768xm<br /> – 16 x Intel® Xeon® 处理器 E7-8890 v4<br /> 384 CPU 内核和 768 CPU 线程 |  24.0 TB |  56 TB | 可用 |

- CPU 内核数 = 服务器单元处理器之和的非超线程 CPU 内核数的总和。
- CPU 线程数 = 服务器单元处理器之和的超线程 CPU 内核数所提供的计算线程总和。 大多数单元都默认配置为使用超线程技术。
- 根据供应商的建议，S768m、S768xm 和 S960m 未配置为使用超线程来运行 SAP HANA。


选择的具体配置取决于工作负荷、CPU 资源和所需的内存。 OLTP 工作负荷可以利用针对 OLAP 工作负荷进行了优化的 SKU。 

除特定于客户的规模调整项目的单元外，产品/服务的硬件基础均通过了 SAP HANA TDI 认证。 两种不同类的硬件将 SKU 分为：

- 称为“I 类”SKU 的 S72、S72m、S96、S144、S144m、S192、S192m 和 S192xm。
- 称为“II 类”SKU 的 S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm 和 S960m。

不会将整个 HANA 大型实例模具以独占方式分配给单个客户使用。 此事实也适用于通过 Azure 中部署的网络结构连接的计算和存储资源的机架。 HANA 大型实例基础结构（例如 Azure）部署在以下三个级别中相互隔离的不同客户&quot;租户&quot;：

- **网络**：在 HANA 大型实例模具中通过虚拟网络实现隔离。
- **存储**：通过分配了存储卷的存储虚拟机实现隔离，并在租户之间隔离存储卷。
- **计算**：专用于单个租户的服务器单元分配。 不对服务器单元进行硬分区或软分区。 租户之间不共享单个服务器或主机。 

不同租户之间的 HANA 大型实例单元部署相互不可见。 在 HANA 大型实例模具级别上，不同租户中部署的 HANA 大型实例单元也无法直接相互通信。 在 HANA 大型实例模具级别上，只有一个租户中的 HANA 大型实例单元才能相互通信。

大型实例模具中部署的每个租户分配给一个 Azure 订阅以进行计费。 但在网络级别，则可从其他 Azure 订阅的虚拟网络进行访问，只要是属于同一 Azure 合约即可。 如果在部署时使用了同一 Azure 区域的另一 Azure 订阅，则也可选择要求隔离的 HANA 大型实例租户。

在 HANA 大型实例上运行 SAP HANA 与在 Azure 中部署的 VM 上运行 SAP HANA 之间有重大差别：

- Azure 上的 SAP HANA（大型实例）没有虚拟化层。 可以利用底层裸机硬件的性能。
- 与 Azure 不同，Azure 上的 SAP HANA（大型实例）服务器专用于特定客户。 服务器单元或主机不可能进行硬分区或软分区。 因此，HANA 大型实例单元作为一个整体分配给租户，并在这种情况下分配。 重新启动或关闭服务器不会自动导致操作系统和 SAP HANA 被部署在另一台服务器上。 （对于 I 类 SKU，唯一的例外是当服务器可能遇到了问题并且需要在另一台服务器上重新部署时。）
- 在 Azure 中，主机处理器类型是根据最佳性价比选择的，与之不同，为 Azure 上的 SAP HANA（大型实例）选择的处理器类型是 Intel E7v3 和 E7v4 处理器系列中性能最高的类型。

**后续步骤**
- 请参阅 [HLI 调整大小](hana-sizing.md)
