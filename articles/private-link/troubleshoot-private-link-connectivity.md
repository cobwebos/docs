---
title: 排查 Azure 专用链接的连接问题
description: 诊断专用链路连接的分步指南
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539461"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>排查 Azure 专用链接的连接问题

本文提供分步指南，用于验证和诊断 Azure 专用链接设置的连接。

使用 Azure 专用链接，可以访问 Azure 平台作为服务 （PaaS） 服务，例如 Azure 存储、Azure 宇宙数据库和 Azure SQL 数据库，以及通过虚拟网络中的专用终结点访问 Azure 托管的客户或合作伙伴服务。 虚拟网络和服务之间的流量通过 Microsoft 骨干网遍历，从而消除了公共 Internet 的暴露。 您还可以在虚拟网络中创建自己的专用链接服务，并私下将其交付给客户。

您可以启用在专用链接访问的 Azure 负载均衡器标准层后面运行的服务。 服务的消费者可以在虚拟网络内创建专用终结点，并将其映射到此服务以私下访问它。

以下是专用链路可用的连接方案：

- 来自同一区域的虚拟网络
- 区域对等虚拟网络
- 全球对等虚拟网络
- 通过 VPN 或 Azure 快速路由电路在本地的客户

## <a name="deployment-troubleshooting"></a>部署故障排除

查看[有关禁用专用链接服务上的网络策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的信息，以排除无法从您为专用链接服务选择的子网中选择的源 IP 地址的情况。

确保禁用为选择要从的源 IP 地址的子网的**专用链接服务网络策略**设置。

## <a name="diagnose-connectivity-problems"></a>诊断连接问题

如果专用链路设置遇到连接问题，请查看这些步骤，以确保所有常规配置都按预期进行。

1. 通过浏览资源查看专用链接配置。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 转到**私人链接中心**。

      ![专用链接中心](./media/private-link-tsg/private-link-center.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在左侧窗格中，选择 **"专用链接服务**"。

      ![专用链接服务](./media/private-link-tsg/private-link-service.png)

    c. 筛选并选择要诊断的专用链接服务。

    d.单击“下一步”。 查看专用终结点连接。
     - 确保从中寻求连接的专用终结点已列出并带有 **"已批准连接"** 状态。
     - 如果状态处于**挂起状态**，请选择它并批准它。

       ![专用终结点连接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 通过选择名称转到要连接的专用终结点。 确保连接状态显示为 **"已批准**"。

       ![专用端点连接概述](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 两侧通过后，请重试连接。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在 **"属性"** 选项卡上的"概述"选项卡和资源**Properties** **ID**上查看**别名**。
     - 确保**别名****和资源 ID**信息与用于创建此服务的专用终结点的**别名****和资源 ID**匹配。

       ![验证别名信息](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![验证资源 ID 信息](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 **"概述"** 选项卡上的**可见性**信息。
     - 确保您的订阅属于**可见性**范围。

       ![验证可见性信息](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. 查看 **"概览"** 选项卡上的**负载均衡器**信息。
     - 您可以通过选择负载均衡器链接转到负载均衡器。

       ![验证负载均衡器信息](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 确保负载均衡器设置已根据您的预期进行配置。
       - 查看**前端 IP 配置**。
       - 查看**后端池**。
       - 查看**负载平衡规则**。

       ![验证负载均衡器属性](./media/private-link-tsg/pls-ilb-properties.png)

     - 确保负载均衡器按以前的设置工作。
       - 在负载均衡器后端池可用的子网以外的任何子网中选择 VM。
       - 尝试从上一个 VM 访问负载均衡器前端。
       - 如果根据负载平衡规则连接到后端池，则负载均衡器可正常工作。
       - 还可以通过 Azure 监视器查看负载均衡器指标，以查看数据是否通过负载均衡器流动。

1. 使用[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)查看数据是否流动。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在专用链接服务资源上，选择**指标**。
     - 选择**字节内**或**字节出**。
     - 查看尝试连接到专用链路服务时数据是否流动。 预计延迟约 10 分钟。

       ![验证专用链接服务指标](./media/private-link-tsg/pls-metrics.png)

1. 如果问题仍未解决且连接问题仍然存在，请与[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)团队联系。

## <a name="next-steps"></a>后续步骤

 * [创建专用链接服务 （CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 专用终结点故障排除指南](troubleshoot-private-endpoint-connectivity.md)
