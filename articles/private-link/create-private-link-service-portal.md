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
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252546"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建专用链接服务

Azure 专用链接服务是指你自己的、由专用链接管理的服务。 可以向专用链接授予对 Azure 标准负载均衡器后面运行的服务或资源的访问权限。 服务的使用者可从其自己的虚拟网络以私密方式访问该服务。 本快速入门介绍如何使用 Azure 门户创建专用链接服务。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-an-internal-load-balancer"></a>创建内部负载均衡器

首先创建虚拟网络。 然后创建一个与专用链接服务配合使用的内部负载均衡器。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在本部分，请创建虚拟网络。 此外，将创建子网来托管用于访问专用链接服务的负载均衡器。

在本部分中，你需要将步骤中的以下参数替换为以下信息：

| 参数                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 美国东部 2      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

使用门户创建标准内部负载均衡器。 指定的名称和 IP 地址自动配置为负载均衡器的前端。

1. 在门户的左上方，选择“创建资源” > “网络” > “负载均衡器”。   

1. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息：  

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **订阅**               | 选择订阅。    |
    | **资源组**         | 从框中选择“myResourceGroupLB”。 |
    | **名称**                   | 输入 **myLoadBalancer**。                                   |
    | **区域**         | 选择“美国东部 2”。                                         |
    | 类型           | 选择“内部”。                                         |
    | **SKU**           | 选择“标准”  。                          |
    | **虚拟网络**           | 选择“myVNet”。                           |
    | IP 地址分配               | 选择“静态”  。   |
    | **专用 IP 地址**|输入一个位于虚拟网络和子网地址空间中的地址。 例如 10.3.0.7。  |

1. 接受剩余设置的默认值，然后选择“查看 + 创建” 

1. 在“查看 + 创建”选项卡上，选择“创建”。  

### <a name="create-standard-load-balancer-resources"></a>创建标准负载均衡器资源

在本部分，我们将为后端地址池和运行状况探测配置负载均衡器设置。 另外，指定负载均衡器规则。

#### <a name="create-a-back-end-pool"></a>创建后端池

后端地址池包含连接到负载均衡器的虚拟 NIC 的 IP 地址。 可通过此池将流量分发到资源。 创建名为 **myBackendPool** 的后端地址池，以包含对流量进行负载均衡的资源。

1. 在最左侧的菜单中，选择“所有服务”。 
1. 选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  
1. 在“设置”下，依次选择“后端池”、“添加”。   
1. 在“添加后端池”页上，键入 **myBackendPool** 作为后端池的名称，然后选择“添加”。  

#### <a name="create-a-health-probe"></a>创建运行状况探测器

使用运行状况探测来允许负载均衡器监视资源状态。 运行状况探测器基于对运行状况检查的资源响应，在负载均衡器中动态添加或删除资源。

若要创建运行状况探测来监视资源的运行状况：

1. 在最左侧的菜单中选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  

1. 在“设置”下，依次选择“运行状况探测”、“添加”。   

1. 在“添加运行状况探测”页上，输入或选择以下值： 

   - **Name**：输入 **myHealthProbe**。
   - **协议**：选择“TCP”  。
   - **端口**：输入 **80**。
   - **时间间隔**：输入 **15**。 此值是探测尝试的间隔秒数。
   - **不正常阈值**：输入 **2**。 此值是将虚拟机视为不正常之前发生的连续探测失败次数。

1. 选择“确定”  。

#### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给资源。 该规则定义：

- 传入流量的前端 IP 配置。
- 接收流量的后端 IP 池。
- 所需的源和目标端口。

名为 **myLoadBalancerRule** 的负载均衡器规则在 **LoadBalancerFrontEnd** 前端中侦听端口 80。 该规则在同一端口 80 上将网络流量发送到 **myBackendPool** 后端地址池。

若要创建负载均衡器规则：

1. 在最左侧的菜单中选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。  

1. 在“设置”下，依次选择“负载均衡规则”、“添加”。   

1. 在“添加负载均衡规则”页上，输入或选择以下值（如果尚不存在这些值）： 

   - **Name**：输入 **myLoadBalancerRule**。
   - **前端 IP 地址：** 输入 **LoadBalancerFrontEnd**。
   - **协议**：选择“TCP”  。
   - **端口**：输入 **80**。
   - **后端端口**：输入 **80**。
   - **后端池**：选择“myBackendPool”。 
   - **运行状况探测**：选择“myHealthProbe”。  

1. 选择“确定”  。

## <a name="create-a-private-link-service"></a>创建专用链接服务

在本部分，你将在标准负载均衡器后面创建专用链接服务。

1. 在 Azure 门户中页面的左上部分，选择“创建资源” > “网络” > “专用链接中心(预览版)”。    也可以使用门户的搜索框来搜索“专用链接”。

1. 在“专用链接中心 - 概述” > “公开自己的服务，使其他人能够连接”中，选择“启动”。   

1. 在“创建专用链接服务 - 基本信息”下，输入或选择以下信息： 

    | 设置           | 值                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | 项目详细信息：  |                                                                              |
    | **订阅**      | 选择订阅。                                                     |
    | **资源组**    | 选择“myResourceGroupLB”。                                                     |
    | 实例详细信息： |                                                                              |
    | **名称**              | 输入 **myPrivateLinkService**。 |
    | **区域**            | 选择“美国东部 2”。                                                         |

1. 在完成时选择“下一步:  出站设置”。

1. 在“创建专用链接服务 - 出站设置”下，输入或选择以下信息： 

    | 设置                           | 值                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **负载均衡器**                     | 选择“myLoadBalancer”。                                                            |
    | **负载均衡器前端 IP 地址** | 选择 **myLoadBalancer** 的前端 IP 地址。                                |
    | **源 NAT 虚拟网络**        | 选择“myVNet”。                                                                    |
    | **源 NAT 子网**                 | 选择“myBackendSubnet”。                                                           |
    | **启用 TCP 代理 v2**               | 根据应用程序是否需要 TCP 代理 v2 标头选择“是”或“否”。   |
    | **专用 IP 地址设置**       | 为每个 NAT IP 配置分配方法和 IP 地址。                  |

1. 在完成时选择“下一步:  访问安全性”。

1. 在“创建专用链接服务 - 访问安全性”下选择“可见性”，然后选择“仅限基于角色的访问控制”。   
  
1. 选择“下一步:  标记” > “查看 + 创建”，或选择页面顶部的“查看 + 创建”选项卡。  

1. 检查信息，然后选择“创建”。 

## <a name="clean-up-resources"></a>清理资源

用完专用链接服务后，请删除资源组以清理本快速入门中使用的资源。

1. 在门户顶部的“搜索”框中输入 **myResourceGroupLB**，然后从搜索结果中选择“myResourceGroupLB”。 
1. 选择“删除资源组”  。
1. 在“键入资源组名称”中，输入 **myResourceGroup**。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建内部 Azure 负载均衡器和专用链接服务。 还可以了解如何[使用 Azure 门户创建专用终结点](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)。
