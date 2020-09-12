---
title: Azure HDInsight 的网络安全组 (NSG) 服务标记
description: 使用 HDInsight 服务标记允许来自运行状况和管理服务节点的群集的入站流量，而无需向你的 Nsg 添加 IP 地址。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 112f915f533627ccdc0ac6efe38caacc80b254bc
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399950"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的 NSG 服务标记

网络安全组 (NSG) 的 Azure HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于尽量降低创建安全规则时的复杂性。 [服务标记](../virtual-network/security-overview.md#service-tags)允许来自特定 IP 的入站流量，而无需输入 NSG 中的每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

HDInsight 服务会管理这些服务标记。 你无法创建自己的服务标记，也无法修改现有标记。 Microsoft 会管理与服务标记匹配的地址前缀，并会在地址发生更改时自动更新服务标记。

如果你想要使用特定的区域，但尚未在此页上记录服务标记，则可以使用 [服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 来查找你的服务标记。 你还可以下载 [服务标记 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 并搜索所需的区域。

## <a name="get-started-with-service-tags"></a>开始使用服务标记

在网络安全组中使用服务标记有两个选项：

- 使用单个全局 HDInsight 服务标记****：此选项会向 HDInsight 服务用来监视所有区域中的群集的所有 IP 地址开放虚拟网络。 此选项是最简单的方法，但如果你有严格的安全要求，它可能不适合。

- 使用多个区域性服务标记****：此选项仅向 HDInsight 在该特定区域中使用的 IP 地址开放虚拟网络。 但是，如果你使用多个区域，则需要向虚拟网络中添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

开始在 HDInsight 群集中使用服务标记的最简单方法是将全局标记 `HDInsight` 添加到 NSG 规则。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的网络安全组。

1. 在“设置”下，依次选择“入站安全规则”、“+ 添加”。**** **** ****

1. 在“源”下拉列表中，选择“服务标记”。**** ****

1. 在“源服务标记”下拉列表中，选择“HDInsight”。**** ****

    ![从 Azure 门户添加服务标记](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此标记包含 HDInsight 可用的所有区域的运行状况和管理服务的 IP 地址。 此标记可确保无论群集创建于何处，群集都可以与必要的运行状况和管理服务通信。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域性 HDInsight 服务标记

如果全局标记选项由于你需要更严格的权限而不可行，则只能允许适用于相应区域的服务标记。 可能有多个服务标记，具体取决于在其中创建群集的区域。

若要了解要为区域添加哪些服务标记，请阅读本文的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域性服务标记

如果群集位于此表中列出的某个区域中，则只需向 NSG 添加一个区域性服务标记。

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| 澳大利亚 | 澳大利亚东部 | HDInsight.AustraliaEast |
| &nbsp; | Australia Southeast | HDInsight.AustraliaSoutheast |
| &nbsp; | 澳大利亚中部 | HDInsight.AustraliaCentral |
| 中国 | 中国东部 2 | HDInsight.ChinaEast2 |
| &nbsp; | 中国北部 2 | HDInsight.ChinaNorth2 |
| United States | 美国中北部 | HDInsight.NorthCentralUS |
| &nbsp; | 美国西部 2 | HDInsight.WestUS2 |
| &nbsp; | 美国中西部 | HDInsight.WestCentralUS |
| 加拿大 | 加拿大东部 | HDInsight.CanadaEast |
| 巴西 | 巴西南部 | HDInsight.BrazilSouth |
| 韩国 | 韩国中部 | HDInsight.KoreaCentral |
| &nbsp; | 韩国南部 | HDInsight.KoreaSouth |
| 印度 | 印度中部 | HDInsight.CentralIndia |
| &nbsp; | 印度南部 | HDInsight.SouthIndia |
| 日本 | 日本西部 | HDInsight.JapanWest |
| 法国 | 法国中部| HDInsight.FranceCentral |
| 英国 | 英国南部 | HDInsight.UKSouth |
| Azure Government | USDoD 中部 | HDInsight.USDoDCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD 东部 | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域性服务标记

如果在上表中未列出创建群集的区域，则需要允许多个区域服务标记。 使用多个的需要是因为不同区域的资源提供程序的排列方式存在差异。

其余区域根据它们使用的区域性服务标记划分为各个组。

#### <a name="group-1"></a>组 1

如果你的群集是在下表的其中一个区域中创建的，则允许服务标记 `HDInsight.WestUS` 和 `HDInsight.EastUS` 。 此外，还列出了区域服务标记。 本部分中的区域需要三个服务标记。

例如，如果你的群集是在该区域中创建的 `East US 2` ，则需要将以下服务标记添加到网络安全组：

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
| &nbsp; | Southeast Asia | HDInsight.SoutheastAsia |
| 澳大利亚 | 澳大利亚东部 | HDInsight.AustraliaEast |

#### <a name="group-2"></a>组 2

*中国北部*和*中国东部*区域中的群集需要允许两个服务标记： `HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast` 。

#### <a name="group-3"></a>组 3

*US Gov 爱荷华州*和*US Gov 弗吉尼亚州*区域中的群集需要允许两个服务标记： `HDInsight.USGovIowa` 和 `HDInsight.USGovVirginia` 。

#### <a name="group-4"></a>组 4

*德国中部*和*德国东北部*地区的群集需要允许两个服务标记： `HDInsight.GermanyCentral` 和 `HDInsight.GermanyNortheast` 。

## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/security-overview.md#security-rules)
- [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
