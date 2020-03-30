---
title: Azure HDInsight 的网络安全组 (NSG) 服务标记
description: 使用 HDInsight 服务标记可以允许从 HDInsight 运行状况和管理服务节点发往群集的入站流量，无需将 IP 地址显式添加到网络安全组。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117230"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的网络安全组 (NSG) 服务标记

网络安全组 (NSG) 的 HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于尽量降低创建安全规则时的复杂性。 [服务标记](../virtual-network/security-overview.md#service-tags)提供了一种替代方法来允许来自特定 IP 地址的入站流量，不需要在网络安全组中输入每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

这些服务标记是由 HDInsight 服务创建和管理的。 你无法创建自己的服务标记，也无法修改现有标记。 Microsoft 会管理与服务标记匹配的地址前缀，并会在地址发生更改时自动更新服务标记。

## <a name="getting-started-with-service-tags"></a>服务标记入门

在网络安全组中使用服务标记有两个选项：

1. 使用单个 HDInsight 服务标记 - 此选项将向 HDInsight 服务用来监视所有区域中群集的所有 IP 地址开放你的虚拟网络。 此选项是最简单的方法，但如果你有严格的安全要求，它可能不适合。

1. 使用多个区域性服务标记 - 此选项将仅向 HDInsight 在该特定区域中使用的 IP 地址开放你的虚拟网络。 但是，如果你使用多个区域，则需要向虚拟网络中添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

开始在 HDInsight 群集中使用服务标记的最简单方法是将全局标记 `HDInsight` 添加到网络安全组规则。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的网络安全组。

1. 在“设置”下，依次选择“入站安全规则”、“+ 添加”。************

1. 在“源”下拉列表中，选择“服务标记”。********

1. 在“源服务标记”下拉列表中，选择“HDInsight”。********

    ![Azure 门户添加服务标记](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此标记包含可使用 HDInsight 的所有区域中运行状况和管理服务的 IP 地址，并确保群集可以与必要的运行状况和管理服务进行通信（无论群集是在哪里创建的）。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域性 HDInsight 服务标记

如果选项 1 由于你需要更严格的权限而不可行，则你只能允许适用于你的区域的服务标记。 适用的服务标记可能是一个、两个或三个服务标记，具体取决于在其中创建群集的区域。

若要了解要为你的区域添加哪些服务标记，请阅读本文档的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域性服务标记

如果你更喜欢服务标记选项 2，并且你的群集位于此表中列出的某个区域中，则只需要向网络安全组中添加单个区域性服务标签。

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| 澳大利亚 | 澳大利亚东部 | HDInsight.AustraliaEast |
| &nbsp; | 澳大利亚东南部 | HDInsight.AustraliaSoutheast |
| &nbsp; | 澳大利亚中部 | HDInsight.AustraliaCentral |
| 中国 | 中国东部 2 | HDInsight.ChinaEast2 |
| &nbsp; | 中国北部 2 | HDInsight.ChinaNorth2 |
| United States | 美国中北部 | HDInsight.NorthCentralUS |
| &nbsp; | 美国西部 2 | HDInsight.WestUS2 |
| &nbsp; | 美国中西部 | HDInsight.WestCentralUS |
| Canada | 加拿大东部 | HDInsight.CanadaEast |
| 巴西 | 巴西南部 | HDInsight.BrazilSouth |
| 韩国 | 韩国中部 | HDInsight.KoreaCentral |
| &nbsp; | 韩国南部 | HDInsight.KoreaSouth |
| 印度 | 印度中部 | HDInsight.CentralIndia |
| &nbsp; | 印度南部 | HDInsight.SouthIndia |
| 日本 | 日本西部 | HDInsight.JapanWest |
| 法国 | 法国中部| HDInsight.FranceCentral |
| 英国 | 英国南部 | HDInsight.UKSouth |
| Azure Government  | USDoD 中部   | HDInsight.USDoDCentral |
| &nbsp; | US Gov 德克萨斯州 | HDInsight.USGovTexas |
| &nbsp; | UsDoD 东部 | HDInsight.USDoDEast |
| &nbsp; | US Gov 亚利桑那州 | HDInsight.USGov亚利桑那 |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域性服务标记

如果你更喜欢服务标记选项 2，并且上面没有列出在其中创建群集的区域，则需要允许多个区域性服务标记。 需要使用多个是因为不同区域的资源提供程序的安排不同。

其余区域根据它们使用的区域性服务标记划分为各个组。

#### <a name="group-1"></a>组 1

如果你的群集是在下表中的某个区域中创建的，则除了列出的区域性服务标记外，还允许服务标记 `HDInsight.WestUS` 和 `HDInsight.EastUS`。 本部分中的区域需要三个服务标记。

例如，如果群集是在 `East US 2` 区域中创建的，则需要将以下服务标记添加到网络安全组：

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| United States | 美国东部 2 | HDInsight.EastUS2 |
| &nbsp; | 美国中部 | HDInsight.CentralUS |
| &nbsp; | 美国中北部 | HDInsight. NorthCentralUS |
| &nbsp; | 美国中南部 | HDInsight.SouthCentralUS |
| &nbsp; | 美国东部 | HDInsight.EastUS |
| &nbsp; | 美国西部 | HDInsight.WestUS |
| 日本 | 日本东部 | HDInsight.JapanEast |
| 欧洲 | 北欧 | HDInsight.NorthEurope |
| &nbsp; | 西欧| HDInsight.WestEurope |
| 亚洲 | 东亚 | HDInsight.EastAsia |
| &nbsp; | 东南亚 | HDInsight.SoutheastAsia |
| 澳大利亚 | 澳大利亚东部 | HDInsight.AustraliaEast |

#### <a name="group-2"></a>组 2

**中国北部**和**中国东部**区域中的群集需要允许两个服务标记：`HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast`。

#### <a name="group-3"></a>组 3

**US Gov 爱荷华州**和 **US Gov 弗吉尼亚州**区域中的群集需要允许两个服务标记：`HDInsight.USGovIowa` 和 `HDInsight.USGovVirginia`。

#### <a name="group-4"></a>组 4

**德国中部**和**德国东北部**区域中的群集需要允许两个服务标记：`HDInsight.GermanyCentral` 和 `HDInsight.GermanyNorthEast`。

## <a name="next-steps"></a>后续步骤

- [网络安全组 - 服务标记](../virtual-network/security-overview.md#security-rules)
- [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
