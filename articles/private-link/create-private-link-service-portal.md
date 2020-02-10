---
title: 快速入门 - 使用 Azure 门户创建专用链接服务
titlesuffix: Azure Private Link
description: 在本快速入门中了解如何使用 Azure 门户创建专用链接服务
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026200"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建专用链接服务

Azure 专用链接服务是对你自己的、由 Azure 专用链接提供支持的服务的引用。 可为 Azure 负载均衡器后面运行的服务或资源启用专用链接访问。 服务的使用者可从其自己的 VNet 以私密方式访问该服务。 本快速入门介绍如何使用 Azure 门户创建专用链接服务。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-an-internal-load-balancer"></a>创建内部负载均衡器

首先创建一个虚拟网络，然后创建一个与 Azure 专用链接服务配合使用的内部负载均衡器。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分，你将创建虚拟网络和子网来托管用于访问专用链接服务的负载均衡器。


1. 在门户的左上方，选择“创建资源”   > “网络”   > “虚拟网络”  。
   
1. 在“创建虚拟网络”窗格中键入或选择以下值： 
   
   - **Name**：键入“MyVNet”  。
   - **ResourceGroup**：选择“新建”，输入 **MyResourceGroupLB**，然后选择“确定”   。 
   - **子网** > **名称**：键入“MyBackendSubnet”  。
   
1. 选择“创建”  。

   ![创建虚拟网络](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

使用门户创建标准内部负载均衡器。 创建的名称和 IP 地址自动配置为负载均衡器的前端。

1. 在门户的左上方，选择“创建资源” > “网络” > “负载均衡器”。   
   
2. 在“创建负载均衡器”页的“基本”选项卡中输入或选择以下信息，接受其余的默认设置，然后选择“查看 + 创建”    ：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 在下拉框中选择“MyResourceGroupLB”。 |
    | 名称                   | *myLoadBalancer*                                   |
    | 区域         | 选择“美国东部 2”。                                         |
    | 类型          | 选择“内部”。                                         |
    | SKU           | 选择“标准”  。                          |
    | 虚拟网络           | 选择“MyVNet”  。                          |    
    | IP 地址分配              | 选择“静态”  。   |
    | 专用 IP 地址|键入一个位于虚拟网络和子网地址空间中的地址，例如 *10.3.0.7*。  |

3. 在“查看 + 创建”选项卡中，单击“创建”   。 
   

### <a name="create-standard-load-balancer-resources"></a>创建标准负载均衡器资源

在本部分中，请为后端地址池和运行状况探测配置负载均衡器设置，并指定负载均衡器规则。

#### <a name="create-a-backend-pool"></a>创建后端池

若要向资源分配流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。 创建后端地址池 *myBackendPool* 以包含用于对流量进行负载均衡的资源。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。   
2. 在“设置”下，依次选择“后端池”、“添加”。   
3. 在“添加后端池”页上，键入 **myBackendPool** 作为后端池的名称，然后选择“添加”。  



#### <a name="create-a-health-probe"></a>创建运行状况探测器

若要允许负载均衡器监视资源状态，请使用运行状况探测。 运行状况探测器基于其对运行状况检查的响应，在负载均衡器中动态添加或删除资源。 

**若要创建运行状况探测来监视资源的运行状况，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。  
   
1. 在“设置”下，依次选择“运行状况探测”、“添加”。   
   
1. 在“添加运行状况探测”  页上，键入或选择以下值：
   
   - **Name**：键入 **MyHealthProbe**。
   - **协议**：下拉并选择“TCP”。  
   - **端口**：键入 **80**。 
   - **时间间隔**：键入 **15**。 时间间隔是两次探测尝试之间的秒数。
   - **不正常阈值**：键入 **2**。 此值是将 VM 视为不正常之前发生的连续探测失败次数。
   
1. 选择“确定”  。

#### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给资源。 此规则定义传入流量的前端 IP 配置、用于接收流量的后端 IP 池，以及所需的源和目标端口。 

名为 **MyLoadBalancerRule** 的负载均衡器规则在前端 **LoadBalancerFrontEnd** 中侦听端口 80。 该规则也在端口 80 上将网络流量发送到后端地址池 **MyBackendPool**。 

**若要创建负载均衡器规则，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。  
   
1. 在“设置”下，依次选择“负载均衡规则”、“添加”。   
   
1. 在“添加负载均衡规则”页上，键入或选择以下值（如果还没有这些值）： 
   
   - **Name**：键入 **MyLoadBalancerRule**。
   - **前端 IP 地址：** 键入 **LoadBalancerFrontEnd**（如果不存在）。
   - **协议**：选择“TCP”  。
   - **端口**：键入 **80**。
   - **后端端口**：键入 **80**。
   - **后端池**：选择“MyBackendPool”  。
   - **运行状况探测**：选择“MyHealthProbe”  。 
   
1. 选择“确定”  。
   
## <a name="create-a-private-link-service"></a>创建专用链接服务

在本部分，你将在标准负载均衡器后面创建专用链接服务。

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “网络” > “专用链接中心(预览版)”。    也可以通过门户搜索功能搜索“专用链接”。

2. 在“专用链接中心 - 概述”中，对“公开自己的服务，使其他人能够连接”选项选择“启动”。  

3. 在“创建专用链接服务 - 基本信息”中，输入或选择以下信息： 

    | 设置           | 值                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **项目详细信息：**  |                                                                              |
    | 订阅      | 选择订阅                                                     |
    | 资源组    | 选择“MyResourceGroupLB”                                                      |
    | **实例详细信息：** |                                                                              |
    | 名称              | 输入 *myPrivateLinkService* |
    | 区域            | 选择“美国东部 2”                                                          |

4. 在完成时选择“下一步:  出站设置”。

5. 在“创建专用链接服务 - 出站设置”中，输入或选择以下信息： 


    | 设置                           | 值                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | 负载均衡器                     | 选择“MyLoadBalancer”                                                            |
    | 负载均衡器前端 IP 地址 | 选择 MyLoadBalancer 的前端 IP 地址                                |
    | 源 NAT 虚拟网络        | 选择“myVNET”                                                                    |
    | 源 NAT 子网                 | 选择“myBackendSubnet”                                                           |
    | 启用 TCP 代理 v2               | 根据应用程序是否需要 TCP 代理 v2 标头选择“是”或“否” |
    | 专用 IP 地址设置       | 为每个 NAT IP 配置分配方法和 IP 地址                  |

6. 在完成时选择“下一步:  访问安全性”。

7. 在“创建专用链接服务 - 访问安全性”中，输入或选择以下信息： 

    | 设置                                     | 值                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | 可见性                                  | 选择“仅限基于角色的访问控制”          |
  
8. 在完成时选择“下一步:  标记”并选择“查看 + 创建”，或选择页面顶部的“查看 + 创建”选项卡。  

9. 检查信息，然后选择“创建”。 

## <a name="clean-up-resources"></a>清理资源 
用完专用链接服务后，请删除资源组以清理本快速入门中使用的资源：

1. 在门户顶部的“搜索”框中输入 *myResourceGroupLB*，然后从搜索结果中选择“myResourceGroupLB”。   。 
2. 选择“删除资源组”  。 
3. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除”   。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建内部 Azure 负载均衡器和专用链接服务。 若要详细了解如何创建专用终结点，请参阅[使用 Azure 门户创建专用终结点](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)。
