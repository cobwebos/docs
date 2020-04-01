---
title: Azure HDInsight 的网络安全组 (NSG) 服务标记
description: 使用 HDInsight 服务标记允许从运行状况和管理服务节点向群集的入站流量，而无需向 NSG 添加 IP 地址。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437678"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的 NSG 服务标记

用于网络安全组 （NSG） 的 Azure HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于尽量降低创建安全规则时的复杂性。 [服务标记](../virtual-network/security-overview.md#service-tags)提供了一种替代方法，用于允许来自特定 IP 地址的入站流量，而无需输入 NSG 中的每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

HDInsight 服务管理这些服务标记。 不能创建自己的服务标记或修改现有标记。 Microsoft 管理与服务标记匹配的地址前缀，并在地址更改时自动更新服务标记。

## <a name="get-started-with-service-tags"></a>开始使用服务标记

在网络安全组中使用服务标记有两个选项：

- **使用单个全局 HDInsight 服务标记**：此选项将虚拟网络打开到 HDInsight 服务用于监视所有区域群集的所有 IP 地址。 此选项是最简单的方法，但如果有限制性的安全要求，则可能不合适。

- **使用多个区域服务标记**：此选项将虚拟网络仅打开 HDInsight 在特定区域使用的 IP 地址。 但是，如果您使用的是多个区域，则需要向虚拟网络添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

开始将服务标记与 HDInsight 群集一起使用的最简单方法是将全局标记`HDInsight`添加到 NSG 规则。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的网络安全组。

1. 在“设置”下，依次选择“入站安全规则”、“+ 添加”。************

1. 在“源”下拉列表中，选择“服务标记”。********

1. 在“源服务标记”下拉列表中，选择“HDInsight”。********

    ![从 Azure 门户添加服务标记](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此标记包含所有可用 HDInsight 的区域的运行状况和管理服务的 IP 地址。 该标记将确保群集可以与必要的运行状况和管理服务通信，无论在何处创建。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域性 HDInsight 服务标记

如果全局标记选项由于需要更严格的权限而不起作用，则只能允许适用于您所在地区的服务标记。 可能有一个、两个或三个适用的服务标记，具体取决于创建群集的区域。

要了解要为区域添加的服务标记，请阅读本文的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域性服务标记

如果您更喜欢使用区域服务标记，并且群集位于此表中列出的区域之一，则只需向网络安全组添加单个区域服务标记。

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
| Azure Government | USDoD 中部 | HDInsight.USDoDCentral |
| &nbsp; | US Gov 德克萨斯州 | HDInsight.USGovTexas |
| &nbsp; | UsDoD 东部 | HDInsight.USDoDEast |
| &nbsp; | US Gov 亚利桑那州 | HDInsight.USGov亚利桑那 |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域性服务标记

如果您更喜欢使用区域服务标记，但创建群集的区域未列在上表中，则需要允许多个区域服务标记。 需要使用多个是因为不同区域的资源提供程序的安排不同。

其余区域根据它们使用的区域性服务标记划分为各个组。

#### <a name="group-1"></a>组 1

如果您的群集是在下表中的一个区域中创建的，则允许服务标记`HDInsight.WestUS`以及`HDInsight.EastUS`列出的区域服务标记。 本部分中的区域需要三个服务标记。

例如，如果群集是在`East US 2`该区域中创建的，则需要向网络安全组添加以下服务标记：

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

*华北*和*华东*地区的群集需要允许两个服务标签：`HDInsight.ChinaNorth`和`HDInsight.ChinaEast`。

#### <a name="group-3"></a>组 3

*美国爱荷华州州长*和*弗吉尼亚州州长*地区的集群需要允许两个服务标签：`HDInsight.USGovIowa`和`HDInsight.USGovVirginia`。

#### <a name="group-4"></a>组 4

*在德国中部*和*德国东北部*地区的群集需要允许两个服务标记：`HDInsight.GermanyCentral`和`HDInsight.GermanyNortheast`。

## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/security-overview.md#security-rules)
- [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
