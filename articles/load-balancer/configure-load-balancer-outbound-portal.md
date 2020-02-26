---
title: 使用 Azure 门户配置负载均衡和出站规则
titleSuffix: Azure Load Balancer
description: 本文说明如何使用 Azure 门户在标准负载均衡器中配置负载平衡和出站规则。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590008"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>使用 Azure 门户在标准负载均衡器中配置负载平衡和出站规则

本文说明如何使用 Azure 门户在标准负载均衡器中配置出站规则。  

负载均衡器资源包含两个前端及其关联的规则。 为入站流量提供一个前端，并为出站流量提供另一个前端。  

每个前端引用一个公共 IP 地址。 在这种情况下，入站流量的公共 IP 地址与出站流量的地址不同。   负载均衡规则仅提供入站负载均衡。 出站规则控制 VM 的出站网络地址转换（NAT）。  

此方案使用两个后端池：一个用于入站流量，一个用于出站流量。 这些池阐释功能并为方案提供灵活性。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-load-balancer"></a>创建负载均衡器

在本部分中，将创建负载均衡器，以便对虚拟机进行负载均衡。 可以创建公共负载均衡器或内部负载均衡器。 创建公共负载均衡器时，将创建一个新的公共 IP 地址，该地址配置为负载均衡器的前端。 默认情况下，前端将命名为**LoadBalancerFrontEnd** 。

1. 在屏幕的左上方，选择“创建资源” **“网络”** “负载均衡器” >  > 。
2. 在 "**创建负载均衡器**" 页的 "**基本**信息" 选项卡中，输入或选择以下信息：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中键入 **myResourceGroupSLB**。|
    | 名称                   | **myLoadBalancer**                                   |
    | 区域         | 选择“西欧”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 选择“标准”或“基本”。 Microsoft 建议将“标准”用于生产工作负荷。 |
    | 公共 IP 地址 | 选择“新建”。 如果要使用现有的公共 IP，请选择 "**使用现有**的"。  现有的公共 IP 必须是**标准**SKU。  基本公共 Ip 与**标准**SKU 负载均衡器不兼容。  |
    | 公共 IP 地址名称              | 在文本框中键入 myPublicIP。|
    | 可用性区域 | 选择 "**区域冗余**"，创建弹性负载均衡器。 若要创建区域负载均衡器，请选择特定的区域：1、2 或 3 |

3. 接受其余配置的默认值。
4. 选择 "**查看 + 创建**"

    > [!IMPORTANT]
    > 本快速入门的其余部分假定在上述 SKU 选择过程中选择了**标准**SKU。

5. 在“查看 + 创建”选项卡中，选择“创建”。   

    ![创建标准负载均衡器](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本部分中，将为后端地址池和运行状况探测配置负载均衡器设置，并指定均衡器规则。

### <a name="create-a-backend-pool"></a>创建后端池

后端地址池包含后端池中的虚拟 Nic 的 IP 地址。 创建后端地址池 **myBackendPool** 以包含用于对 Internet 流量进行负载均衡的虚拟机。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“后端池”、“添加”。
3. 在“添加后端池”页上，键入 **myBackendPool** 作为后端池的名称，然后选择“添加”。

### <a name="create-a-health-probe"></a>创建运行状况探测器

运行状况探测用于监视应用的状态。 运行状况探测根据其对运行状况检查的响应，从负载均衡器中添加或删除 Vm。 创建运行状况探测 **myHealthProbe** 以监视 VM 的运行状况。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“运行状况探测”、“添加”。
    
    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 **myHealthProbe**。 |
    | 协议 | 选择“HTTP”。 |
    | 端口 | 输入 **80**。|
    | 时间间隔 | 输入 **15** 作为两次探测尝试之间的**时间间隔**（以秒为单位）。 |
    | 不正常阈值 | 选择“2”，作为将 VM 视为不正常所要达到的**不正常阈值**或连续探测失败次数。|
    | | |
4. 选择“确定”。

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则
负载均衡器规则用于定义将流量分配给 VM 的方式。 

你定义：
 - 传入流量的前端 IP 配置。
 - 用于接收流量的后端 IP 池。
 - 所需的源和目标端口。 

在下一部分中，你将创建：
 - 用于侦听端口80的负载均衡器规则**myHTTPRule** 。
 - 前端**LoadBalancerFrontEnd**。
 - 后端地址池**myBackEndPool**也使用端口80。 

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“负载均衡规则”、“添加”。
3. 使用以下值配置负载均衡规则：
    
    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 **myHTTPRule**。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。|
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。|
    | 运行状况探测 | 选择“myHealthProbe”。 |
    | 创建隐式出站规则 | 请选择“否”。 在后面的部分中，我们将使用专用的公共 IP 创建出站规则。 |
4. 将剩余的字段保留默认设置，然后选择“确定”。

## <a name="create-outbound-rule-configuration"></a>创建出站规则配置
负载均衡器出站规则配置后端池中的 Vm 的出站 SNAT。 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>创建出站公共 IP 地址和前端

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在 "**设置**" 下，选择 "**前端 IP 配置**"，然后选择 "**添加**"。

3. 使用以下值为出站配置前端 IP 配置：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入**LoadBalancerFrontEndOutbound**。 |
    | IP 版本 | 选择 " **IPv4**"。 |
    | IP 类型 | 选择 " **IP 地址**"。|
    | 公共 IP 地址 | 选择“新建”。 在 "**添加公共 IP 地址**" 中，输入**myPublicIPOutbound**。  选择“确定”。 |

4. 选择 **添加** 。

### <a name="create-an-outbound-backend-pool"></a>创建出站后端池

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“后端池”、“添加”。

3. 在 "**添加后端池**" 页上，为 "名称" 键入**myBackendPoolOutbound**，作为后端池的名称，然后选择 "**添加**"。

### <a name="create-outbound-rule"></a>创建出站规则

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在 "**设置**" 下，选择 "**出站规则**"，然后选择 "**添加**"。

3. 使用这些值来配置出站规则：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入**myOutboundRule**。 |
    | 前端 IP 地址 | 选择**LoadBalancerFrontEndOutbound**。 |
    | 空闲超时（分钟） | 将滑块移动到 * * 15 分钟。|
    | TCP 重置 | 选择“启用”。|
    | 后端池 | 选择**myBackendPoolOutbound** |
    | 端口分配-> 端口分配 | 选择 "**手动选择出站端口数**" |
    | 出站端口-> 选择 | 选择**每个实例的端口** |
    | 出站端口-每个实例 > 端口 | 输入**10000**。 |

4. 选择 **添加** 。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 选择包含负载均衡器的资源组 " **myResourceGroupSLB** "，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

本文内容：
 - 已创建标准负载均衡器。
 - 配置入站和出站负载平衡器流量规则。
 - 为后端池中的 Vm 配置运行状况探测。 

若要了解详细信息，请继续阅读[有关 Azure 负载均衡器的教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。
