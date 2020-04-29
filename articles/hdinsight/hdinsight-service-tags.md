---
title: Azure HDInsight 的网络安全组 (NSG) 服务标记
description: 使用 HDInsight 服务标记允许来自运行状况和管理服务节点的群集的入站流量，而无需向你的 Nsg 添加 IP 地址。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 5608d0cd83e506bc6b30337db5148f344f59f80e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410853"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>适用于 Azure HDInsight 的 NSG 服务标记

网络安全组（Nsg）的 Azure HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于尽量降低创建安全规则时的复杂性。 [服务标记](../virtual-network/security-overview.md#service-tags)允许来自特定 ip 的入站流量，无需在 nsg 中输入每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

HDInsight 服务管理这些服务标记。 不能创建自己的服务标记或修改现有标记。 Microsoft 管理与服务标记相匹配的地址前缀，并在地址更改时自动更新服务标记。

## <a name="get-started-with-service-tags"></a>服务标记入门

在网络安全组中使用服务标记有两个选项：

- **使用单个全局 HDInsight 服务标记**：此选项会将你的虚拟网络打开到 HDInsight 服务用来在所有区域中监视群集的所有 IP 地址。 此选项是最简单的方法，但如果你具有限制性安全要求，则可能不适合使用此选项。

- **使用多个区域服务标记**：此选项可将虚拟网络仅打开 HDInsight 在该特定区域中使用的 IP 地址。 但是，如果使用多个区域，则需要向虚拟网络添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

若要在 HDInsight 群集中开始使用服务标记，最简单的方法是将全局`HDInsight`标记添加到 NSG 规则。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的网络安全组。

1. 在“设置”下，依次选择“入站安全规则”、“+ 添加”。   

1. 在“源”下拉列表中，选择“服务标记”。  

1. 在“源服务标记”下拉列表中，选择“HDInsight”。  

    ![从 Azure 门户添加服务标记](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此标记包含 HDInsight 可用的所有区域的运行状况和管理服务的 IP 地址。 标记将确保在创建群集时，群集可以与必要的运行状况和管理服务进行通信。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域性 HDInsight 服务标记

如果由于需要更严格的权限而导致全局标记选项不起作用，则只能允许适用于你所在区域的服务标记。 可能有多个服务标记，具体取决于创建群集的区域。

若要确定要为你的区域添加哪些服务标记，请阅读本文的以下各节。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域性服务标记

如果你的群集位于此表中列出的区域，只需向 NSG 添加一个区域服务标记。

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
| &nbsp; | US Gov 亚利桑那州 | USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域性服务标记

如果在上表中未列出创建群集的区域，则需要允许多个区域服务标记。 使用多个的需要是因为不同区域的资源提供程序的排列方式存在差异。

其余区域根据它们使用的区域性服务标记划分为各个组。

#### <a name="group-1"></a>组 1

如果你的群集是在下表的其中一个区域中创建的，则允许服务`HDInsight.WestUS`标记`HDInsight.EastUS`和。 此外，还列出了区域服务标记。 本部分中的区域需要三个服务标记。

例如，如果你的群集是在该`East US 2`区域中创建的，则需要将以下服务标记添加到网络安全组：

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

*中国北部*和*中国东部*区域中的群集需要允许两个服务标记： `HDInsight.ChinaNorth`和。 `HDInsight.ChinaEast`

#### <a name="group-3"></a>组 3

*US Gov 爱荷华州*和*US Gov 弗吉尼亚州*区域中的群集需要允许两个服务标记： `HDInsight.USGovIowa`和`HDInsight.USGovVirginia`。

#### <a name="group-4"></a>组 4

*德国中部*和*德国东北部*地区的群集需要允许两个服务标记： `HDInsight.GermanyCentral`和。 `HDInsight.GermanyNortheast`

## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/security-overview.md#security-rules)
- [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
