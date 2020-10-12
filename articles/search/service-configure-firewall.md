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
ms.openlocfilehash: 3e8a94b6b9b71d2d71b634edd70ea4150652b143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932791"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>为 Azure 认知搜索配置 IP 防火墙

Azure 认知搜索支持“入站防火墙支持”的 IP 规则。 此模型为搜索服务提供了额外的安全层，类似于 Azure 虚拟网络安全组中的 IP 规则。 利用这些 IP 规则，可以配置为仅允许从一组已批准的计算机和/或云服务访问搜索服务。 从这些已批准的计算机和服务访问搜索服务中存储的数据仍需调用方提供有效的授权令牌。

> [!Important]
> 可以使用 Azure 门户或[管理 REST API 版本 2020-03-13](/rest/api/searchmanagement/) 来配置 Azure 认知搜索服务上的 IP 规则。

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> 使用 Azure 门户配置 IP 防火墙

若要在 Azure 门户中设置 IP 访问控制策略，请转到 Azure 认知搜索服务页，然后在导航菜单中选择“网络”。 终结点网络连接必须为公共连接。 如果连接设置为“专用”，则只能通过专用终结点访问搜索服务。

![显示如何在 Azure 门户中配置 IP 防火墙的屏幕截图](./media/service-configure-firewall/azure-portal-firewall.png)

Azure 门户提供了以 CIDR 格式指定 IP 地址和 IP 地址范围的功能。 CIDR 表示法的示例是 8.8.8.0/24，它表示范围从 8.8.8.0 到 8.8.8.255 的 IP。

> [!NOTE]
> 为 Azure 认知搜索服务启用 IP 访问控制策略后，将拒绝从 IP 地址范围允许列表中不包含的计算机向数据平面发出的所有请求。 配置 IP 规则时，将禁用 Azure 门户的某些功能。 你将能够查看和管理服务级别信息，但出于安全方面的考虑，已限制对索引数据以及此服务中的各种组件（如索引、索引器和技能集定义）的门户访问。

### <a name="requests-from-your-current-ip"></a>来自当前 IP 的请求

为简化开发，Azure 门户将帮助你识别客户端计算机的 IP 并将其添加到允许列表中。 然后，计算机上运行的应用可以访问你的 Azure 认知搜索服务。

门户将自动检测客户端 IP 地址。 它可能是计算机或网络网关的客户端 IP 地址。 请务必在将工作负荷置于生产环境之前删除此 IP 地址。

若要将当前 IP 添加到 IP 列表，请选择“添加客户端 IP 地址”。 再选择“保存”。

![显示如何将 IP 防火墙设置配置为允许当前 IP 的屏幕截图](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>排查 IP 访问控制策略的问题

可使用以下选项排查 IP 访问控制策略的问题：

### <a name="azure-portal"></a>Azure 门户

为 Azure 认知搜索服务启用 IP 访问控制策略会阻止来自 IP 地址范围允许列表以外的计算机（包括 Azure 门户）的所有请求。  你将能够查看和管理服务级别信息，但出于安全方面的考虑，已限制对索引数据以及此服务中的各种组件（如索引、索引器和技能集定义）的门户访问。 

### <a name="sdks"></a>SDK

使用 SDK 从不在允许列表内的计算机访问 Azure 认知搜索服务时，将返回一般的“403 禁止访问”响应，但不提供其他任何详细信息。 验证帐户的允许 IP 列表并确保已为搜索服务更新了正确的配置。

## <a name="next-steps"></a>后续步骤

若要详细了解如何通过专用链接访问搜索服务，请参阅以下文章：

* [创建用于与 Azure 认知搜索建立安全连接的专用终结点](service-create-private-endpoint.md)