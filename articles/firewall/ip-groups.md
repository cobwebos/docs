---
title: Azure 防火墙中的 IP 组
description: IP 组允许您对 Azure 防火墙规则的 IP 地址进行分组和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: e0638cbccd5e3bc282dbdd7d3b5918e29081a12b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757162"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 防火墙中的 IP 组（预览）

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 组允许您通过以下方式对 Azure 防火墙规则的 IP 地址进行分组和管理：

- 作为 DNAT 规则中的源地址
- 作为网络规则中的源地址或目标地址
- 作为应用程序规则中的源地址


IP 组可以具有单个 IP 地址、多个 IP 地址或一个或多个 IP 地址范围。

IP 组可以在 Azure 防火墙 DNAT、网络和应用程序规则中重复使用，用于跨 Azure 区域和订阅的多个防火墙。 组名称必须是唯一的。 您可以在 Azure 门户、Azure CLI 或 REST API 中配置 IP 组。 提供了一个示例模板，以帮助您入门。

## <a name="sample-format"></a>示例格式

以下 IPv4 地址格式示例在 IP 组中有效：

- 单地址： 10.0.0.0
- CIDR 表示法： 10.1.0.0/32
- 地址范围： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>创建 IP 组

可以使用 Azure 门户、Azure CLI 或 REST API 创建 IP 组。 有关详细信息，请参阅创建[IP 组（预览）。](create-ip-group.md)

## <a name="browse-ip-groups"></a>浏览 IP 组
1. 在 Azure 门户搜索栏中，键入**IP 组**并选择它。 您可以看到 IP 组的列表，也可以选择 **"添加"** 以创建新的 IP 组。
2. 选择 IP 组以打开概览页面。 您可以编辑、添加或删除 IP 地址或 IP 组。

   ![IP 组概述](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 组

您可以看到 IP 组中的所有 IP 地址及其关联的规则或资源。 要删除 IP 组，必须首先将 IP 组与使用它的资源分离。

1. 要查看或编辑 IP 地址，请在左侧窗格的 **"设置"** 下选择**IP 地址**。
2. 要添加单个或多个 IP 地址，请选择 **"添加 IP 地址**"。 这将打开"**拖动"或"浏览"** 页面进行上传，或者您可以手动输入地址。
3.    选择编辑或删除 IP 地址右侧的省略号 （**...** 要编辑或删除多个 IP 地址，请选择框，然后选择顶部的 **"编辑**"或 **"删除**"。
4. 最后，可以导出 CSV 文件格式的文件。

> [!NOTE]
> 如果在 IP 组中仍在使用规则时删除 IP 组中的所有 IP 地址，则跳过该规则。


## <a name="use-an-ip-group"></a>使用 IP 组

现在，在创建 Azure 防火墙 DNAT、应用程序或网络规则时，可以选择**IP 组**作为 IP 地址的**源类型**或**目标类型**。

> [!NOTE]
> 防火墙策略中不支持 IP 组。 它目前仅受传统防火墙规则的支持。

![防火墙中的 IP 组](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>上市区域

IP 组在所有公共云区域都可用。

## <a name="ip-address-limits"></a>IP 地址限制

对于 50 个或更少的 IP 组，每个防火墙实例最多可以有 5000 个单独的 IP 地址。 对于 51 到 100 个 IP 组，每个防火墙实例可以有 500 个单独的 IP 地址。

### <a name="examples"></a>示例

#### <a name="example-1-supported"></a>示例 1：受支持

|IP 组  |• IP 地址  |表示法  |规则  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Rule1|
|IPGroup3     |1|1.2.3.4|Rule1|
|     |**总计 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>示例 2：受支持

|IP 组  |• IP 地址  |表示法  |规则  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Rule1|
|     |**总计 8192**|         |         |

#### <a name="example-3-not-supported"></a>示例 3：不支持

|IP 组  |• IP 地址  |表示法  |规则  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Rule1|
|     |**总计 8192**|||

#### <a name="example-4-supported"></a>示例 4：受支持

|IP 组  |• IP 地址  |表示法  |规则  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Rule2|
|     |**总计 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>相关 Azure 电源外壳 cmdlet

以下 Azure PowerShell cmdlet 可用于创建和管理 IP 组：

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [删除-AzIP 组](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新-Az防火墙网络规则](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新-Aa防火墙](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。