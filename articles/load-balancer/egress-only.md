---
title: 仅出站负载平衡器配置
titleSuffix: Azure Load Balancer
description: 本文介绍如何使用出站 NAT 创建内部负载均衡器
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: b44f626546b313299701687157b37b7df021bd61
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038180"
---
# <a name="outbound-only-load-balancer-configuration"></a>仅出站负载平衡器配置

结合使用内部和外部标准负载均衡器为内部负载均衡器后面的 Vm 创建出站连接。 

此配置为内部负载均衡器方案提供出站 NAT，并为后端池生成 "仅出站" 安装程序。


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="虚拟网络 NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*图：仅出口负载平衡器配置*

所需步骤如下：

1. 使用堡垒主机创建虚拟网络。
2. 创建只有专用 IP 的虚拟机。
3. 创建内部和公共标准负载均衡器。
4. 将后端池添加到负载均衡器，并将 VM 放入每个池。
5. 通过堡垒主机连接到 VM，并执行以下操作：
    1. 测试出站连接、 
    2. 配置公共负载均衡器上的出站规则。
    3. 重新测试出站连接。

## <a name="create-virtual-network-and-virtual-machine"></a>创建虚拟网络和虚拟机

创建包含两个子网的虚拟网络：

* 主子网
* 堡垒子网

在新的虚拟网络中创建虚拟机。

### <a name="create-the-virtual-network"></a>创建虚拟网络

1. [登录](https://portal.azure.com) Azure 门户。

2. 在屏幕的左上方，选择 "**创建资源" > 网络 > "虚拟网络**" 或在搜索框中搜索 "**虚拟网络**"。

2. 在 "**创建虚拟网络**" 中，在 "**基本**信息" 选项卡中输入或选择以下信息：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 输入**myResourceGroupLB**。 </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入**myVNet**                                    |
    | 区域           | 选择**美国东部 2** |

3. 选择 " **Ip 地址**" 选项卡，或选择页面底部的 " **ip 地址**" 按钮。

4. 在 " **IP 地址**" 选项卡中输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入**10.1.0.0/16** |

5. 在 "**子网名称**" 下，选择 word**默认值**。

6. 在 "**编辑子网**" 中，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入**myBackendSubnet** |
    | 子网地址范围 | 输入**10.1.0.0/24** |

7. 选择“保存”。

8. 选择“安全”**** 选项卡。

9. 在**BastionHost**下，选择 "**启用**"。 输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 堡垒名称 | 输入**myBastionHost** |
    | AzureBastionSubnet 地址空间 | 输入**10.1.1.0/24** |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于 "**名称**"，请输入**myBastionIP**。 </br> 选择“确定”。 |


8. 选择 "**查看**" 和 "创建" 选项卡或选择 "查看" 和 "**创建**" 按钮。

9. 选择“创建” 。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroupLB” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入**myVM** |
    | 区域 | 选择**美国东部 2** |
    | 可用性选项 | 选择**不需要基础结构冗余** |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |  |
    | 公共入站端口 | 选择 "**允许所选端口**" |
    | 选择入站端口 | 选择**RDP (3389) ** |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-----|------------|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择**无**|
    | 将此虚拟机放置在现有负载平衡解决方案的后面？ | 请选择“否” |
   
5. 选择“管理”选项卡，或者选择“下一步” > “管理”。

6. 在“管理”选项卡中，选择或输入：
    
    | 设置 | 值 |
    |-|-|
    | **Monitoring** |  |
    | 启动诊断 | 选择“关闭” |
   
7. 选择“查看 + 创建”。 
  
8. 检查设置，然后选择“创建”。

## <a name="create-load-balancers-and-test-connectivity"></a>创建负载均衡器和测试连接

使用 Azure 门户创建：

* 内部负载均衡器
* 公共负载均衡器
 
将创建的 VM 添加到每个的后端池。  然后，将设置一个配置，以便仅允许来自 VM 的出站连接，并在之前和之后进行测试。

### <a name="create-internal-load-balancer"></a>创建内部负载均衡器

1. 在屏幕的左上方，选择“创建资源” > “网络” > “负载均衡器”  。

2. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择上一步中创建的**myResourceGroupLB** 。|
    | 名称                   | 输入**myInternalLoadBalancer**                                   |
    | 区域         | 选择“美国东部 2”。                                        |
    | 类型          | 选择“内部”。                                        |
    | SKU           | 选择“标准” |
    | 虚拟网络 | 选择上一步中创建的**myVNet** 。 |
    | 子网  | 选择上一步中创建的**myBackendSubnet** 。 |
    | IP 地址分配 | 选择 "**动态**"。 |

3. 接受剩余设置的默认值，然后选择“查看 + 创建”。

4. 在“查看 + 创建”选项卡中，选择“创建”。   

### <a name="create-public-load-balancer"></a>创建公共负载均衡器

1. 在屏幕的左上方，选择“创建资源” > “网络” > “负载均衡器”  。

2. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中输入 myResourceGroupLB 。|
    | 名称                   | 输入**myPublicLoadBalancer**                                   |
    | 区域         | 选择“美国东部 2”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 选择“标准” |
    | 公共 IP 地址 | 选择“新建”。 |
    | 公共 IP 地址名称 | 在文本框中输入**myFrontendIP** 。|
    | 可用性区域 | 选择**区域冗余** |
    | 添加一个公共 IPv6 地址 | 请选择“否”。 |

3. 接受剩余设置的默认值，然后选择“查看 + 创建”。

4. 在“查看 + 创建”选项卡中，选择“创建”。   
   
### <a name="create-internal-backend-address-pool"></a>创建内部后端地址池

创建后端地址池**myInternalBackendPool**：

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后从 "资源" 列表中选择 " **myInternalLoadBalancer** "。

2. 在“设置”下，依次选择“后端池”、“添加”。

3. 在 "**添加后端池**" 页上，为 "名称" 键入**myInternalBackendPool**，作为后端池的名称。
 
4. 在“虚拟网络”中，选择“myVNet”。

5. 在 "**虚拟机**" 下，选择 "**添加**" 并选择 " **myVM**"。
 
6. 选择 "**添加**"。

### <a name="create-public-backend-address-pool"></a>创建公用后端地址池

创建后端地址池**myPublicBackendPool**：

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后从 "资源" 列表中选择 " **myPublicLoadBalancer** "。

2. 在“设置”下，依次选择“后端池”、“添加”。

3. 在 "**添加后端池**" 页上，为 "名称" 键入**myPublicBackendPool**，作为后端池的名称。

4. 在“虚拟网络”中，选择“myVNet”。
 
5. 在 "**虚拟机**" 下，选择 "**添加**" 并选择 " **myVM**"。
 
6. 选择 "**添加**"。

### <a name="test-connectivity-before-outbound-rule"></a>在出站规则之前测试连接

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后在 "资源" 列表中，选择位于**MyResourceGroupLB**资源组中的**myVM** 。

2. 在 "**概述**" 页上，选择 "**连接**"，然后选择 "**堡垒**"。

4. 输入在 VM 创建过程中输入的用户名和密码。

5. 选择“连接”。

6. 打开 Internet Explorer。

7. **https://whatsmyip.org**在地址栏中输入。

8. 连接应失败。 默认情况下，标准公共负载均衡器不[允许出站流量，无需定义出站规则](load-balancer-overview.md#securebydefault)。
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>创建公共负载均衡器出站规则

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后从 "资源" 列表中选择 " **myPublicLoadBalancer** "。

2. 在“设置”下，依次选择“出站规则”、“添加”。

3. 使用以下值配置出站规则：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myOutboundRule。 |
    | 前端 IP 地址 | 选择**LoadBalancerFrontEnd**。|
    | 空闲超时（分钟） | 将滑块移动到 15 分钟。|
    | TCP 重置 | 选择“启用”。|
    | 后端池 | 选择**myPublicBackendPool**。| |
    | 端口分配 -> 端口分配 | 选择 **"使用默认的出站端口数"** |

4. 选择 **添加** 。

### <a name="test-connectivity-after-outbound-rule"></a>出站规则后测试连接

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后在 "资源" 列表中，选择位于**MyResourceGroupLB**资源组中的**myVM** 。

2. 在 "**概述**" 页上，选择 "**连接**"，然后选择 "**堡垒**"。

4. 输入在 VM 创建过程中输入的用户名和密码。

5. 选择“连接”。

6. 打开 Internet Explorer。

7. **https://whatsmyip.org**在地址栏中输入。

8. 连接应成功。

9. 显示的 IP 地址应为**myPublicLoadBalancer**的前端 ip 地址。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器、VM 和所有相关资源，请将其删除。 

为此，请选择资源组 " **myResourceGroupLB** "，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已创建一个 "仅出站" 配置，其中包含公共负载均衡器和内部负载均衡器的组合。  

此配置允许你将传入的内部流量负载平衡到后端池，同时仍然阻止任何公用入站连接。

- 了解[Azure 负载均衡器](load-balancer-overview.md)。
- 了解[Azure 中的出站连接](load-balancer-outbound-connections.md)。
- 负载均衡器[常见问题解答](load-balancer-faqs.md)。
- 了解[Azure 堡垒](https://docs.microsoft.com/azure/bastion/bastion-overview)
