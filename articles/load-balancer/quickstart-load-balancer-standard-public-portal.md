---
title: 快速入门：创建公共负载均衡器 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure 门户创建负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1864ce5a3c1b5b0b2e0cfe757e66fca2074b764c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475798"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共负载均衡器，以便对 VM 进行负载均衡

使用 Azure 门户创建公共负载均衡器和三个虚拟机，通过这种方式开始使用 Azure 负载均衡器。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

---

# <a name="option-1-default-create-a-public-load-balancer-standard-sku"></a>[选项 1（默认）：创建公共负载均衡器（标准 SKU）](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。  有关 sku 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。 

可以创建公共负载均衡器或内部负载均衡器。 

创建公共负载均衡器时，还会为该负载均衡器创建一个配置为前端（默认情况下命名为“LoadBalancerFrontend”）的新公共 IP 地址。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “负载均衡器”  。

2. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | Value                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中输入 myResourceGroupLB 。|
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“西欧”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 选择“标准” |
    | 公共 IP 地址 | 选择“新建”。 若要使用现有的公共 IP，请选择“使用现有项”。 |
    | 公共 IP 地址名称 | 在文本框中键入 myPublicIP。|
    | 可用性区域 | 选择“区域冗余”以创建弹性负载均衡器。 若要创建区域负载均衡器，请选择特定的区域：1、2 或 3 |
    | 添加一个公共 IPv6 地址 | 请选择“否”。 </br> 有关 IPv6 地址和负载均衡器的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 IPv6？](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. 接受剩余设置的默认值，然后选择“查看 + 创建”。

4. 在“查看 + 创建”选项卡中，选择“创建”。   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="创建标准负载均衡器" border="true":::
 
## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本部分中，你将配置：

* 后端地址池的负载均衡器设置。
* 运行状况探测。
* 负载均衡器规则。

### <a name="create-a-backend-pool"></a>创建后端池

后端地址池包含连接到负载均衡器的虚拟 (NIC) 的 IP 地址。 

创建后端地址池 **myBackendPool** 以包含用于对 Internet 流量进行负载均衡的虚拟机。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“后端池”、“添加”。

3. 在“添加后端池”页上，键入 **myBackendPool** 作为后端池的名称，然后选择“添加”。

### <a name="create-a-health-probe"></a>创建运行状况探测器

负载均衡器使用运行状况探测器监视应用的状态。 

运行状况探测器基于 VM 对运行状况检查的响应，在负载均衡器中添加或删除 VM。 

创建名为 **myHealthProbe** 的运行状况探测来监视 VM 的运行状况。

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

3. 将剩余的字段保留默认设置，然后选择“确定”。

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量。 源端口和目标端口在规则中定义。 

在本部分中，你将创建创建负载均衡器规则：

* 该规则名为“myHTTPRule”。
* 在名为“LoadBalancerFrontEnd”的前端中。
* 正在侦听“端口 80”。
* 将负载均衡流量定向到“端口 80”上名为“myBackendPool”的后端 。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“负载均衡规则”、“添加”。

3. 使用以下值配置负载均衡规则：
    
    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 **myHTTPRule**。 |
    | IP 版本 | 选择“IPv4” |
    | 前端 IP 地址 | 选择“LoadBalancerFrontEnd” |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。|
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。|
    | 运行状况探测 | 选择“myHealthProbe”。 |
    | 创建隐式出站规则 | 请选择“否”。

4. 将剩余的字段保留默认设置，然后选择“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

本部分的操作：

* 创建虚拟网络。
* 为负载均衡器的后端池创建三个虚拟机。
* 在虚拟机上安装 IIS 以测试负载均衡器。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在本部分中，你会将步骤中的参数替换为以下信息：

| 参数                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西欧      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>创建虚拟机

公共 IP SKU 和负载均衡器 SKU 必须匹配。 公共 IP SKU 和负载均衡器 SKU 必须匹配。 

在本部分中，你将创建三个 VM（“myVM1”、“myVM2”和“myVM3”），在三个不同的区域（“区域 1”、“区域 2”和“区域 3”）中使用标准的公共 IP 地址     。 

这些 VM 将添加到先前创建的负载均衡器的后端池中。

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | “值”                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroupLB” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM1” |
    | 区域 | 选择“西欧” |
    | 可用性选项 | 选择“可用性区域” |
    | 可用性区域 | 选择“1” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | “值” |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 接受“myVM-ip”的默认值。 </br> IP 将自动成为区域 1 中的标准 SKU IP。 |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 选择“新建”。 </br> 在“创建网络安全组”中，在“名称”中输入“myNSG”  。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“目标端口范围”下，输入“80” 。 </br> 在“优先级”下，输入“100” 。 </br> 在“名称”中，输入“myHTTPRule” </br> 选择“添加” </br> 选择“确定” |
    | **负载均衡**  |
    | 是否将此虚拟机置于现有负载均衡解决方案之后？ | 选择“是” |
    | **负载均衡设置** |
    | 负载均衡选项 | 选择“Azure 负载均衡” |
    | 选择负载均衡器 | 选择“myLoadBalancer”  |
    | 选择后端池 | 选择“myBackendPool” |

5. 选择“管理”选项卡，或者选择“下一步” > “管理”。

6. 在“管理”选项卡中，选择或输入：
    
    | 设置 | “值” |
    |-|-|
    | **Monitoring** |  |
    | 启动诊断 | 选择“关闭” |
   
7. 选择“查看 + 创建”。 
  
8. 检查设置，然后选择“创建”。

9. 按照第 1 到第 8 步，使用以下值创建两个 VM，所有其他设置与 myVM1 相同：

    | 设置 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名称 |  **myVM2** |**myVM3**|
    | 可用性区域 | **2** |**3**|
    | 网络安全组 | 选择现有的“myNSG”| 选择现有的“myNSG”|

## <a name="create-outbound-rule-configuration"></a>创建出站规则配置
负载均衡器出站规则为后端池中的 VM 配置出站 SNAT。 

有关出站连接的详细信息，请参阅 [Azure 中的出站连接](load-balancer-outbound-connections.md)。

### <a name="create-outbound-rule"></a>创建出站规则

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“出站规则”、“添加”。

3. 使用以下值配置出站规则：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myOutboundRule。 |
    | 前端 IP 地址 | 选择“新建”。 </br> 在“名称”中输入“LoadBalancerFrontEndOutbound”。 </br> 选择“IP 地址”或“IP 前缀”。 </br> 在“公共 IP 地址”或“公共 IP 前缀”下选择“新建”。 </br> 对于“名称”，请输入“myPublicIPOutbound”或“myPublicIPPrefixOutbound”。 </br> 选择“确定” 。 </br> 选择“添加”  。|
    | 空闲超时（分钟） | 将滑块移动到 15 分钟。|
    | TCP 重置 | 选择“启用”。|
    | 后端池 | 选择“新建”。 </br> 在“名称”中输入“myBackendPoolOutbound”。 </br> 选择 **添加** 。 |
    | 端口分配 -> 端口分配 | 选择“手动选择出站端口数” |
    | 出站端口 -> 选择方式 | 选择“每个实例的端口数” |
    | 出站端口 -> 每个实例的端口数 | 输入“10000”。 |

4. 选择 **添加** 。

### <a name="add-virtual-machines-to-outbound-pool"></a>向出站池添加虚拟机

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  

2. 在“设置”下，选择“后端池”。 

3. 选择“myBackendPoolOutbound”。

4. 在“虚拟网络”中，选择“myVNet”。

5. 在“虚拟机”中，选择“+ 添加”。

6. 选中“myVM1”、“myVM2”和“myVM3”旁边的框。 

7. 选择“添加”  。

8. 选择“保存” 。

# <a name="option-2-create-a-public-load-balancer-basic-sku"></a>[选项 2：创建公共负载均衡器（基本 SKU）](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。  有关 sku 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。 

可以创建公共负载均衡器或内部负载均衡器。 

创建公共负载均衡器时，还会为该负载均衡器创建一个配置为前端（默认情况下命名为“LoadBalancerFrontend”）的新公共 IP 地址。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “负载均衡器”  。

2. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | “值”                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中键入 myResourceGroupLB 。|
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“西欧”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 选择“基本” |
    | 公共 IP 地址 | 选择“新建”。 若要使用现有的公共 IP，请选择“使用现有项”。 |
    | 公共 IP 地址名称 | 在文本框中键入 myPublicIP。|
    | 分配 | 选择“动态” |
    | 添加一个公共 IPv6 地址 | 请选择“否”。 </br> 有关 IPv6 地址和负载均衡器的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 IPv6？](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. 接受剩余设置的默认值，然后选择“查看 + 创建”。

4. 在“查看 + 创建”选项卡中，选择“创建”。   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="创建基本负载均衡器" border="true":::

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本部分中，你将配置：

* 创建虚拟网络。
* 后端地址池的负载均衡器设置。
* 运行状况探测。
* 负载均衡器规则。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在本部分中，你会将步骤中的参数替换为以下信息：

| 参数                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西欧      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>创建后端池

后端地址池包含连接到负载均衡器的虚拟 (NIC) 的 IP 地址。 

创建后端地址池 **myBackendPool** 以包含用于对 Internet 流量进行负载均衡的虚拟机。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  

2. 在“设置”下，依次选择“后端池”、“添加”。

3. 在“添加后端池”页上，输入或选择：
    
    | 设置 | Value |
    | ------- | ----- |
    | 名称 | 输入“myBackendPool”。 |
    | 虚拟网络 | 选择“myVNet”。 |
    | 关联到 | 选择“虚拟机” |

### <a name="create-a-health-probe"></a>创建运行状况探测器

负载均衡器使用运行状况探测器监视应用的状态。 

运行状况探测器基于 VM 对运行状况检查的响应，在负载均衡器中添加或删除 VM。 

创建名为 **myHealthProbe** 的运行状况探测来监视 VM 的运行状况。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“运行状况探测”、“添加”。
    
    | 设置 | Value |
    | ------- | ----- |
    | 名称 | 输入 **myHealthProbe**。 |
    | 协议 | 选择“HTTP”。 |
    | 端口 | 输入 **80**。|
    | 路径 | 输入“/” |
    | 时间间隔 | 输入 **15** 作为两次探测尝试之间的**时间间隔**（以秒为单位）。 |
    | 不正常阈值 | 选择“2”，作为将 VM 视为不正常所要达到的**不正常阈值**或连续探测失败次数。|

3. 选择“确定” 。

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量。 源端口和目标端口在规则中定义。 

在本部分中，你将创建创建负载均衡器规则：

* 该规则名为“myHTTPRule”。
* 在名为“LoadBalancerFrontEnd”的前端中。
* 正在侦听“端口 80”。
* 将负载均衡流量定向到“端口 80”上名为“myBackendPool”的后端 。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。

2. 在“设置”下，依次选择“负载均衡规则”、“添加”。

3. 使用以下值配置负载均衡规则：
    
    | 设置 | Value |
    | ------- | ----- |
    | 名称 | 输入 **myHTTPRule**。 |
    | IP 版本 | 选择“IPv4” |
    | 前端 IP 地址 | 选择“LoadBalancerFrontEnd” |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。|
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。|
    | 运行状况探测 | 选择“myHealthProbe”。 |
 
4. 将剩余的字段保留默认设置，然后选择“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

本部分的操作：

* 为负载均衡器的后端池创建三个虚拟机。
* 创建虚拟机的可用性集。
* 在虚拟机上安装 IIS 以测试负载均衡器。

### <a name="create-virtual-machines"></a>创建虚拟机

公共 IP SKU 和负载均衡器 SKU 必须匹配。 对于基本负载均衡器，请使用后端池中具有基本 IP 地址的 VM。 

在本部分中，你将创建三个具有基本公共 IP 地址的 VM（“myVM1”、“myVM2”和“myVM3”）  。  

这三个 VM 将添加到名为“myAvailabilitySet”的可用性集中。

这些 VM 将添加到先前创建的负载均衡器的后端池中。

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroupLB” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM1” |
    | 区域 | 选择“西欧” |
    | 可用性选项 | 选择“可用性集” |
    | 可用性集 | 选择“新建”。 </br> 在“名称”中输入 myAvailabilitySet 。 </br> 选择“确定” |
    | 映像 | Windows Server 2019 Datacenter |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择 myVNet |
    | 子网 | 选择“myBackendSubnet” |
    | 公共 IP | 选择“新建” </br> 在“名称”中输入“myVM-ip”。 </br> 选择“确定” |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 选择“新建”。 </br> 在“创建网络安全组”中，在“名称”中输入“myNSG”  。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“目标端口范围”下，输入“80” 。 </br> 在“优先级”下，输入“100” 。 </br> 在“名称”中，输入“myHTTPRule” </br> 选择“添加” </br> 选择“确定” |
    | **负载均衡**  |
    | 是否将此虚拟机置于现有负载均衡解决方案之后？ | 请选择“否” |
 
5. 选择“管理”选项卡，或者选择“下一步” > “管理”。

6. 在“管理”选项卡中，选择或输入：
    
    | 设置 | 值 |
    |---|---|
    | **Monitoring** | |
    | 启动诊断 | 选择“关闭” |

7. 选择“查看 + 创建”。 
  
8. 检查设置，然后选择“创建”。

9. 按照第 1 到第 8 步，使用以下值创建两个 VM，所有其他设置与 myVM1 相同：

    | 设置 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名称 |  **myVM2** |**myVM3**|
    | 可用性集| 选择“myAvailabilitySet” | 选择“myAvailabilitySet”|
    | 网络安全组 | 选择现有的“myNSG”| 选择现有的“myNSG”|

### <a name="add-virtual-machines-to-the-backend-pool"></a>向后端池添加虚拟机

必须将在之前步骤中创建的 VM 添加到 myLoadBalancer 的后端池中。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  

2. 在“设置”下，依次选择“后端池”和“myBackendPool”  。

3. 在“关联到”下，选择“虚拟机” 。

4. 在“虚拟机”部分，选择“+添加” 。

5. 选中“myVM1”、“myVM2”和“myVM3”旁边的框。

6. 选择“添加”  。

7. 选择“保存” 。

---

## <a name="install-iis"></a>安装 IIS

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择位于“myResourceGroupLB”资源组中的“myVM1”。

2. 在“概述”页面上，选择“连接”，为虚拟机下载 RDP 文件。

3. 打开该 RDP 文件。

4. 使用在创建此 VM 过程中提供的凭据登录到 VM。

5. 在服务器桌面上，导航到“Windows 管理工具”>“Windows PowerShell”。

6. 在 PowerShell 窗口中，运行以下命令以：

    * 安装 IIS 服务器
    * 删除默认的 iisstart.htm 文件
    * 添加显示 VM 名称的新 iisstart.htm 文件：

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
7. 关闭与 **myVM1** 之间的 RDP 会话。

8. 重复步骤 1 到步骤 6，在 **myVM2** 和 **myVM3** 上安装 IIS 和已更新的 iisstart.htm 文件。

## <a name="test-the-load-balancer"></a>测试负载均衡器

1. 在“概述”屏幕上找到负载均衡器的公共 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myPublicIP”。

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

   ![IIS Web 服务器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

要查看所有三个 VM 中的负载均衡器分配流量，可以自定义每个 VM 的 IIS Web 服务器的默认页面，然后从客户端计算机强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源组、负载均衡器和所有相关资源，请将其删除。 为此，请选择包含资源的资源组“myResourceGroupSLB”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，请执行以下操作：

* 已创建 Azure 标准或基本负载均衡器
* 已将 3 个 VM 连接到负载均衡器。
* 已配置负载均衡器流量规则、运行状况探测器，然后测试负载均衡器。 

若要了解有关 Azure 负载均衡器的更多信息，请进一步阅读[什么是 Azure 负载均衡器？](load-balancer-overview.md)和[负载均衡器常见问题](load-balancer-faqs.md)。

详细了解[负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。
