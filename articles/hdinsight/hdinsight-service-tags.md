---
title: 适用于 Azure HDInsight 的网络安全组（NSG）服务标记
description: 使用 HDInsight 服务标记允许来自 HDInsight 运行状况和管理服务节点的到群集的入站流量，而无需将 IP 地址显式添加到网络安全组。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/19/2019
ms.openlocfilehash: 7e3ce33bdf0773ababe5eb190877a9288c094c5c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187080"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>适用于 Azure HDInsight 的网络安全组（NSG）服务标记

网络安全组（Nsg）的 HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于最大程度地降低安全规则创建的复杂性。 [服务标记](../virtual-network/security-overview.md#service-tags)提供了一种替代方法，允许来自特定 IP 地址的入站流量，而无需输入网络安全组中的每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

这些服务标记由 HDInsight 服务创建和管理。 不能创建自己的服务标记，也不能修改现有标记。 Microsoft 管理与服务标记相匹配的地址前缀，并在地址更改时自动更新服务标记。

## <a name="getting-started-with-service-tags"></a>服务标记入门

使用网络安全组中的服务标记有两个选项：

1. 使用单个 HDInsight 服务标记-此选项会将你的虚拟网络打开到 HDInsight 服务使用的所有 IP 地址，以便在所有区域中监视群集。 此选项是最简单的方法，但如果你具有限制性安全要求，则可能不适合使用此选项。

1. 使用多个区域服务标记-此选项会将你的虚拟网络仅打开 HDInsight 在该特定区域中使用的 IP 地址。 但是，如果使用多个区域，则需要向虚拟网络添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

若要在 HDInsight 群集中开始使用服务标记，最简单的方法是将全局标记 `HDInsight` 添加到网络安全组规则。 有关如何将服务标记添加到网络安全组的说明，请参阅[安全组：服务标记](../virtual-network/security-overview.md#service-tags)。

此标记包含 HDInsight 可用的所有区域的运行状况和管理服务的 IP 地址，并确保无论在何处创建群集，群集都可以与必要的运行状况和管理服务通信。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域 HDInsight 服务标记

如果选项1不起作用，因为你需要更严格的权限，则可以仅允许适用于你所在区域的服务标记。 适用的服务标记可以是一个、两个或三个服务标记，具体取决于创建群集的区域。

若要找出要为你的区域添加哪些服务标记，请阅读本文档的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域服务标记

如果你更愿意使用服务标记选项2，并且你的群集位于此表中列出的某个区域，则你只需将单个区域服务标记添加到你的网络安全组。

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| 澳大利亚 | 澳大利亚东部 | AustraliaEast |
| &nbsp; | 澳大利亚东南部 | AustraliaSoutheast |
| &nbsp; | 澳大利亚中部 | AustraliaCentral |
| 中国 | 中国东部 2 | ChinaEast2 |
| &nbsp; | 中国北部 2 | ChinaNorth2 |
| 美国 | 美国中北部 | NorthCentralUS |
| &nbsp; | 美国西部 2 | WestUS2 |
| &nbsp; | 美国中西部 | WestCentralUS |
| 加拿大 | 加拿大东部 | CanadaEast |
| 巴西 | 巴西南部 | BrazilSouth |
| 韩国 | 韩国中部 | KoreaCentral |
| &nbsp; | 韩国南部 | KoreaSouth |
| 印度 | 印度中部 | CentralIndia |
| &nbsp; | 印度南部 | SouthIndia |
| 日本 | 日本西部 | JapanWest |
| 法国 | 法国中部| FranceCentral |
| 英国 | 英国南部 | UKSouth |
| Azure 政府版（Fairfax） | USDoD 中部   | USDoDCentral |
| &nbsp; | 美国德克萨斯州政府 | USGovTexas |
| &nbsp; | UsDoD 东部 | USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域服务标记

如果你更愿意使用服务标记选项2，并且上面未列出在其中创建群集的区域，则需要允许多个区域服务标记。 使用多个的需要是因为不同区域的资源提供程序的排列方式存在差异。

其余区域根据它们所使用的区域服务标记划分为多个组。

#### <a name="group-1"></a>组1

如果你的群集是在下表的其中一个区域中创建的，则除了列出的区域服务标记外，还允许 `HDInsight.WestUS` 和 `HDInsight.EastUS` 服务标记。 本部分中的区域需要三个服务标记。

例如，如果群集是在 `East US 2` 区域创建的，则需要将以下服务标记添加到网络安全组：

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| 美国 | 美国东部 2 | EastUS2 |
| &nbsp; | 美国中部 | CentralUS |
| &nbsp; | NorthCentral | HDInsight. NorthCentralUS |
| &nbsp; | 美国中南部 | Default-machinelearning-southcentralus |
| &nbsp; | 美国东部 | EastUS |
| &nbsp; | 美国西部 | WestUS |
| 日本 | 日本东部 | JapanEast |
| 欧洲 | 北欧 | NorthEurope |
| &nbsp; | 西欧| WestEurope |
| 亚洲 | 东亚 | EastAsia |
| &nbsp; | 东南亚 | SoutheastAsia |
| 澳大利亚 | 澳大利亚东部 | AustraliaEast |

#### <a name="group-2"></a>组2

**中国北部**和**中国东部**区域中的群集需要允许两个服务标记： `HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast`。

#### <a name="group-3"></a>组3

**US Gov 爱荷华州**和**US Gov 弗吉尼亚州**区域中的群集需要允许两个服务标记： `HDInsight.USGovIowa` 和 `HDInsight.USGovVirginia`。

#### <a name="group-4"></a>组4

**德国中部**和**德国**北部地区的群集需要允许两个服务标记： `HDInsight.GermanyCentral` 和 `HDInsight.GermanyNorthEast`。

## <a name="next-steps"></a>后续步骤

* [网络安全组-服务标记](../virtual-network/security-overview.md#security-rules)
* [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
