---
title: 为 Azure 认知搜索服务配置 IP 防火墙
titleSuffix: Azure Cognitive Search
description: 配置 IP 控制策略，以限制对 Azure 认知搜索服务的访问。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 699715e1188616c2d6bda47016ec1ea7b05cef83
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125568"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>为 Azure 认知搜索配置 IP 防火墙

Azure 认知搜索支持入站防火墙支持的 IP 规则。 此模型为搜索服务提供了额外的安全层，类似于你将在 Azure 虚拟网络安全组中发现的 IP 规则。 使用这些 IP 规则，你可以将搜索服务配置为只能从一组已批准的计算机和/或云服务进行访问。 从这些已批准的计算机和服务集合访问搜索服务中存储的数据仍将要求调用方提供有效的授权令牌。

> [!Important]
> 可以使用 Azure 门户或[管理 REST API 版本 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/)来配置 Azure 认知搜索服务上的 IP 规则。

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a>使用 Azure 门户配置 IP 防火墙

若要在 Azure 门户中设置 IP 访问控制策略，请访问 Azure 认知搜索服务页，然后在导航菜单上选择 "**网络**"。 终结点网络连接必须是**公共**的。 如果连接设置为 "**专用**"，则只能通过专用终结点访问搜索服务。

![显示如何在 Azure 门户中配置 IP 防火墙的屏幕截图](./media/service-configure-firewall/azure-portal-firewall.png)

Azure 门户提供了指定 CIDR 格式的 IP 地址和 IP 地址范围的功能。 CIDR 表示法的示例是 8.8.8.0/24，它表示范围从8.8.8.0 到8.8.8.255 的 Ip。

> [!NOTE]
> 为 Azure 认知搜索服务启用 IP 访问控制策略后，将拒绝从 IP 地址范围允许列表外的计算机对数据平面的所有请求。 配置 IP 规则时，将禁用 Azure 门户的某些功能。 你将能够查看和管理服务级别信息，但出于安全考虑，出于安全原因，对索引数据和服务中的各种组件（如索引、索引器和技能组合定义）的门户访问是受限的。

### <a name="requests-from-your-current-ip"></a>来自当前 IP 的请求

为简化开发，Azure 门户将帮助你识别客户端计算机的 IP 并将其添加到允许列表中。 然后，在计算机上运行的应用可以访问 Azure 认知搜索服务。

门户会自动检测客户端 IP 地址。 它可能是计算机或网络网关的客户端 IP 地址。 在将工作负荷投入生产之前，请确保删除此 IP 地址。

若要将当前 IP 添加到 Ip 列表，请选中 "**添加客户端 IP 地址**"。 选择“保存”  。

![显示如何将 IP 防火墙设置配置为允许当前 IP 的屏幕截图](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>排查 IP 访问控制策略的问题

可使用以下选项排查 IP 访问控制策略的问题：

### <a name="azure-portal"></a>Azure 门户

为 Azure 认知搜索服务启用 IP 访问控制策略会阻止来自 IP 地址范围允许列表的计算机发出的所有请求，包括 Azure 门户。  你将能够查看和管理服务级别信息，但出于安全考虑，出于安全原因，对索引数据和服务中的各种组件（如索引、索引器和技能组合定义）的门户访问是受限的。 

### <a name="sdks"></a>SDK

当使用 SDK 从不在允许列表中的计算机访问 Azure 认知搜索服务时，将返回通用的**403 禁止**响应，无其他详细信息。 验证你的帐户的允许的 IP 列表，并确保为你的搜索服务更新了正确的配置。

## <a name="next-steps"></a>后续步骤

若要详细了解如何通过专用链接访问搜索服务，请参阅以下文章：

* [创建用于与 Azure 认知搜索建立安全连接的专用终结点](service-create-private-endpoint.md)
