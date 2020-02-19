---
title: Azure 防火墙中的 IP 组
description: IP 组允许对 Azure 防火墙规则的 IP 地址进行分组和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444480"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 防火墙中的 IP 组（预览版）

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 组允许通过以下方式对 Azure 防火墙规则的 IP 地址进行分组和管理：

- 作为 DNAT 规则中的源地址
- 作为网络规则中的源或目标地址
- 作为应用程序规则中的源地址


IP 组可以有单个 IP 地址、多个 IP 地址或一个或多个 IP 地址范围。

对于 Azure 中的区域和订阅，可以在 Azure 防火墙 DNAT、网络和应用程序规则中重复使用 IP 组。 组名称必须是唯一的。 可以在 Azure 门户、Azure CLI 或 REST API 中配置 IP 组。 提供了一个示例模板，帮助你入门。

## <a name="sample-format"></a>示例格式

以下 IPv4 地址格式示例有效，可在 IP 组中使用：

- 单个地址：10.0.0。0
- CIDR 表示法： 10.1.0.0/32
- 地址范围： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>创建 IP 组

可以使用 Azure 门户、Azure CLI 或 REST API 创建 IP 组。 有关详细信息，请参阅[创建 IP 组（预览版）](create-ip-group.md)。

## <a name="browse-ip-groups"></a>浏览 IP 组
1. 在 Azure 门户搜索栏中，键入 " **IP 组**" 并将其选中。 你可以看到 IP 组的列表，或者可以选择 "**添加**" 以创建新的 ip 组。
2. 选择 IP 组以打开 "概述" 页。 可以编辑、添加或删除 IP 地址或 IP 组。

   ![IP 组概述](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 组

你可以查看 IP 组中的所有 IP 地址以及与其关联的规则或资源。 若要删除 IP 组，必须先将该 IP 组与使用该 ip 组的资源取消关联。

1. 若要查看或编辑 IP 地址，请在左窗格中的 "**设置**" 下选择 " **ip 地址**"。
2. 若要添加单个或多个 IP 地址，请选择 "**添加 Ip 地址**"。 这会打开用于上传的**拖动或浏览**页，也可以手动输入该地址。
3.  选择右侧的省略号（ **...** ）可编辑或删除 IP 地址。 若要编辑或删除多个 IP 地址，请选中相应的框，然后选择顶部的 "**编辑**" 或 "**删除**"。
4. 最后，可以导出 CSV 文件格式的文件。

> [!NOTE]
> 如果删除 IP 组中的所有 IP 地址，但在规则中仍在使用它，则会跳过该规则。


## <a name="use-an-ip-group"></a>使用 IP 组

你现在可以在创建 Azure 防火墙 DNAT、应用程序或网络规则时选择**Ip 组**作为 ip 地址的**源类型**或**目标类型**。

> [!NOTE]
> 防火墙策略不支持 IP 组。 目前仅支持传统防火墙规则。

![防火墙中的 IP 组](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>上市区域

IP 组目前在以下区域中提供：

- 美国西部
- 美国西部 2
- 美国东部
- 美国东部 2
- 美国中部
- 美国中北部
- 美国中西部
- 美国中南部
- 加拿大中部
- 北欧
- 西欧
- 法国中部
- 英国南部
- 澳大利亚东部
- 澳大利亚中部
- 澳大利亚东南部

## <a name="related-azure-powershell-cmdlets"></a>相关 Azure PowerShell cmdlet

以下 Azure PowerShell cmdlet 可用于创建和管理 IP 组：

- [新-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。