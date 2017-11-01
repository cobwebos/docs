---
title: "针对 Azure 中的多个 IP 配置进行负载均衡 | Microsoft Docs"
description: "在主要和辅助 IP 配置间进行负载均衡。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecb64aa13b3b08f7b054a0665df3dc0cdb3e09bd
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>使用 Azure 门户对多个 IP 配置进行负载均衡

> [!div class="op_single_selector"]
> * [门户](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

本文介绍如何对辅助网络接口控制器 (NIC) 上的多个 IP 地址使用 Azure 负载均衡器。 下图演示了本文所述的方案：

![负载均衡器方案](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

此方案使用以下配置：

- 两个运行 Windows 的虚拟机 (VM)。
- 每个 VM 包含一个主要 NIC 和一个辅助 NIC。
- 每个辅助 NIC 有两个 IP 配置。
- 每个 VM 托管两个网站：contoso.com 和 fabrikam.com。
- 每个网站已绑定到辅助 NIC 上的某个 IP 配置。
- Azure 负载均衡器用于公开两个前端 IP 地址，每个地址对应于一个网站。 前端地址用于将流量分配到每个网站的相应 IP 配置。
- 前端 IP 地址和后端池 IP 地址使用相同的端口号。

## <a name="prerequisites"></a>先决条件

方案示例假设已创建名为 **contosofabrikam** 的资源组，该资源组的配置如下：

- 该资源组包含名为的 **myVNet** 虚拟网络。
- **myVNet** 包含名为 **VM1** 和 **VM2** 的两个 VM 网络。
- VM1 和 VM2 位于名为 **myAvailset** 的同一个可用性集中。 
- VM1 和 VM2 各有一个主要 NIC，分别名为 **VM1NIC1** 和 **VM2NIC1**。 
- VM1 和 VM2 各有一个辅助 NIC，分别名为 **VM1NIC2** 和 **VM2NIC2**。

有关创建具有多个 NIC 的 VM 的详细信息，请参阅[使用 PowerShell 创建具有多个 NIC 的 VM](../virtual-machines/windows/multiple-nics.md)。

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>针对多个 IP 配置执行负载均衡

完成以下步骤，实现本文中所述的方案：

### <a name="step-1-configure-the-secondary-nics"></a>步骤 1：配置辅助 NIC

对于虚拟网络中的每个 VM，请添加辅助 NIC 的 IP 配置：  

1. 浏览到 Azure 门户：http://portal.azure.com。使用 Azure 帐户登录。

2. 在屏幕左上方，选择“资源组”图标。 然后选择 VM 所在的资源组（例如 **contosofabrikam**）。 “资源组”窗格中显示了 VM 的所有资源和 NIC。

3. 针对每个 VM 的辅助 NIC，请添加 IP 配置：

    1. 选择要配置的辅助 NIC。
    
    2. 选择“IP 配置”。 在下一个窗格中的顶部附近，选择“添加”。

    3. 在“添加 IP 配置”下，将第二个 IP 配置添加到 NIC： 

        1. 输入辅助 IP 配置的名称。 （例如，对于 VM1 和 VM2，分别将 IP 配置命名为 **VM1NIC2-ipconfig2** 和 **VM2NIC2-ipconfig2**）。

        2. 对于“专用 IP 地址”>“分配”设置，请选择“静态”。

        3. 选择“确定”。

完成辅助 NIC 的第二个 IP 配置后，它会显示在给定 NIC 的“IP 配置”设置下。

### <a name="step-2-create-the-load-balancer"></a>步骤 2：创建负载均衡器

为配置创建负载均衡器：

1. 浏览到 Azure 门户：http://portal.azure.com。使用 Azure 帐户登录。

2. 在屏幕左上方，选择“新建” > “网络” > “负载均衡器”。 接下来，选择“创建”。

3. 在“创建负载均衡器”下，为负载均衡器键入一个名称。 此方案使用名称 **mylb**。

4. 在“公共 IP 地址”下，创建名为 **PublicIP1** 的新公共 IP。

5. 在“资源组”下，选择 VM 的现有资源组（例如 **contosofabrikam**）。 选择负载均衡器要部署到的位置，再选择“确定”。

负载均衡器随即开始部署。 部署可能需要几分钟才能成功完成。 部署完成后，负载均衡器将作为资源显示在资源组中。

### <a name="step-3-configure-the-front-end-ip-pool"></a>步骤 3：配置前端 IP 池

针对每个网站（contoso.com 和 fabrikam.com），请在负载均衡器上配置前端 IP 池：

1. 在门户中选择“更多服务”。 在筛选框中键入“公共 IP 地址”，选择“公共 IP 地址”。 在下一个窗格中的顶部附近，选择“添加”。

2. 为两个网站（contoso.com 和 fabrikam.com）配置两个公共 IP 地址（**PublicIP1** 和 **PublicIP2**）：

    1. 键入前端 IP 地址的名称。

    2. 对于“资源组”，请选择 VM 的现有资源组（例如 **contosofabrikam**）。

    3. 对于“位置”，请选择 VM 所在的同一位置。

    4. 选择“确定”。

    创建公共 IP 地址后，它们会显示在“公共 IP 地址”下。

3. <a name="step3-3"></a>在门户中选择“更多服务”。 在筛选框中键入“负载均衡器”，选择“负载均衡器”。 

4. 选择要将前端 IP 池添加到的负载均衡器 (**mylb**)。

5. 在“设置”下，选择“前端池”。 在下一个窗格中的顶部附近，选择“添加”。

6. 键入前端 IP 地址的名称（例如 **contosofe** 或 **fabrikamfe**）。

7. <a name="step3-7"></a>选择“IP 地址”。 在“选择公共 IP 地址”下选择前端的 IP 地址（**PublicIP1** 或 **PublicIP2**）。

8. 重复本部分中的<a href="#step3-3">步骤 3</a> 至<a href="#step3-7">步骤 7</a>，创建第二个前端 IP 地址。

配置前端池后，IP 地址会显示在负载均衡器的“前端 IP 池”设置下。 
    
### <a name="step-4-configure-the-back-end-pool"></a>步骤 4：配置后端池

针对每个网站（contoso.com 和 fabrikam.com），请在负载均衡器上配置后端地址池：
        
1. 在门户中选择“更多服务”。 在筛选框中键入“负载均衡器”，选择“负载均衡器”。

2. 选择要将后端池添加到的负载均衡器 (**mylb**)。

3. 在“设置”下，选择“后端池”。 键入后端池的名称（例如 **contosopool** 或 **fabrikampool**）。 在下一个窗格中的顶部附近，选择“添加”。 

4. 对于“关联到”，请选择“可用性集”。

5. 对于“可用性集”，请选择“myAvailset”。

6. 为两个 VM 添加目标网络 IP 配置： 

    ![为负载均衡器配置后端池](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. 对于“目标虚拟机”，请选择要添加到后端池的 VM（例如 **VM1** 或 **VM2**）。

    2. 对于“网络 IP 配置”，请选择在前一步骤中为 VM 的辅助 NIC 选择的 IP 配置（例如 **VM1NIC2-ipconfig2** 或 **VM2NIC2-ipconfig2**）。

7. 选择“确定”。

配置后端池后，地址会显示在负载均衡器的“后端池”设置下。

### <a name="step-5-configure-the-health-probe"></a>步骤 5：配置运行状况探测

为负载均衡器配置运行状况探测

1. 在门户中选择“更多服务”。 在筛选框中键入“负载均衡器”，选择“负载均衡器”。

2. 选择要将运行状况探测添加到的负载均衡器 (**mylb**)。

3. 在“设置”下，选择“运行状况探测”。 在下一个窗格中的顶部附近，选择“添加”。 

4. 键入运行状况探测的名称（例如 **HTTP**）。 选择“确定”。

### <a name="step-6-configure-load-balancing-rules"></a>步骤 6：配置负载均衡规则

针对每个网站（contoso.com 和 fabrikam.com），请配置负载均衡规则：
    
1. <a name="step6-1"></a>在“设置”下，选择“运行状况探测”。 在下一个窗格中的顶部附近，选择“添加”。 

2. 对于“名称”，请键入负载均衡规则的名称（例如，为 contoso.com 键入 **HTTPc**，为 fabrikam.com 键入 **HTTPf**）。

3. 对于“前端 IP 地址”，请选择前面创建的前端 IP 地址（例如 **contosofe** 或 **fabrikamfe**）。

4. 对于“端口”和“后端端口”，请保留默认值 **80**。

5. 对于“浮动 IP (直接服务器返回)”，请选择“已启用”。

6. <a name="step6-6"></a>选择“确定”。

7. 重复本部分中的<a href="#step6-1">步骤 1</a> 至<a href="#step6-6">步骤 6</a>，创建第二个负载均衡器规则。

配置规则后，它们会显示在负载均衡器的“负载均衡规则”设置下。

### <a name="step-7-configure-dns-records"></a>步骤 7：配置 DNS 记录

最后一个步骤是，将 DNS 资源记录配置为指向负载均衡器的相应前端 IP 地址。 可以在 Azure DNS 中托管域。 有关将 Azure DNS 与负载均衡器配合使用的详细信息，请参阅[将 Azure DNS 与其他 Azure 服务配合使用](../dns/dns-for-azure-services.md)。

## <a name="next-steps"></a>后续步骤
- 若要深入了解如何在 Azure 中结合使用负载均衡服务，请参阅[在 Azure 中使用负载均衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- 若要了解如何使用不同类型的日志对负载均衡器进行管理和故障排除，请参阅 [Azure 负载均衡器的 Log Analytics](../load-balancer/load-balancer-monitor-log.md)。
