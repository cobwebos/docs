---
title: 创建并关联服务终结点策略 - Azure 门户
titlesuffix: Azure Virtual Network
description: 在本文中了解如何在 Azure 门户中设置和关联服务终结点策略。
services: virtual-network
documentationcenter: virtual-network
author: anithaa
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: aaa07759ed8b10578d024d5838ac1d2658778695
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999890"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>使用 Azure 门户创建、更改或删除服务终结点策略

借助服务终结点策略，可越过服务终结点筛选虚拟网络流量，将范围限制到特定的 Azure 资源。 如果不熟悉服务终结点策略，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)了解详细信息。

 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建服务终结点策略
> * 创建服务终结点策略定义
> * 创建带子网的虚拟网络
> * 将服务终结点策略关联到子网中

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-service-endpoint-policy"></a>创建服务终结点策略

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 在搜索窗格中，键入“服务终结点策略”，选择“服务终结点策略(预览版)”，然后选择“创建”。
3. 在“基本信息”中，输入或选择以下信息 

   - 订阅：选择用于策略的订阅。    
   - 资源组：选择“新建”，并输入 myResourceGroup     
   - 名称：myEndpointPolicy
   - 位置：美国中西部     
 
   ![“创建服务终结点策略”基本信息](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. 在“策略定义”中，输入或选择以下信息

   - 单击“+添加资源”，输入或选择以下信息，再接受其余设置的默认值并单击“添加”。  
   - 范围：选择“单个帐户”、“订阅中的所有帐户”或“资源组中的所有帐户”。    
   - 订阅：选择用于存储帐户的订阅。 策略和存储帐户可位于不同的订阅。   
   - 资源组：选择你的资源组。 如果范围设置为“资源组中的所有帐户”或“单个帐户”，则必填。  
   - 资源：mystorageaccountportal    
   - 单击“+添加资源”，以继续添加更多资源。
   
   ![创建服务终结点策略定义](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. 可选：在“标记”中，输入或选择以下信息：
   
   - 密钥：选择策略的键。 例如：部门     
   - 值：输入该键的值对。 例如：财务

6. 选择“查看 + 创建”。 验证信息并单击“创建”。 要做进一步的编辑，请单击“上一步”。 

   ![“创建服务终结点策略”最终验证](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>查看服务终结点策略 

1. 在门户的“所有服务”框中，开始键入“服务终结点策略”。 选择“服务终结点策略(预览版)”。
2. 在“订阅”下，选择你的订阅和资源组，如下图中所示

   ![显示策略](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. 选择策略，再单击“策略定义”以查看或添加更多策略定义。

   ![显示策略定义](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. 选择“关联的子网”以查看与策略关联的子网。 要将策略关联到子网，请单击“导航到同一区域中的虚拟网络”。

   ![显示关联子网](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>将策略关联到子网

>[!WARNING] 
> 请确保在关联策略之前，对所选服务而言，通过子网访问的所有资源均已添加到该策略中。 关联策略后，对服务的终结点区域而言，仅可访问该策略中列出的资源。 

在将策略关联到子网之前，必须创建虚拟网络和子网，然后才可将此策略关联到子网：

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 在“创建虚拟网络”下输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：
   - 名称：myVirtualNetwork      
   - 地址空间：10.0.0.0/16      
   - 订阅：选择订阅。 策略应与 VNet 位于同一订阅中     
   - 资源组：选择“使用现有”，然后选择“myResourceGroup”     
   - 位置：美国中西部     
   - 子网名称：private     
   - 地址范围：10.0.0.0/24
     
4. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myVirtualNetwork”。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
5. 在“设置”下选择“子网”，然后选择“private”。
6. 下图中所示，依次选择“服务终结点”、Microsoft.Storage、“服务终结点策略”和 myEndpointPolicy，然后选择“保存”：

   ![关联策略](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>根据网络安全组 (NSG)，将可通过此子网访问其他区域中的服务资源。 要限制到仅可访问终结点区域，请将 NSG 限制为只有终结点区域中的服务流量。 要详细了解如何使用服务标记按区域创建 NSG，请参阅 [NSG Azure 服务标记](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)。

在下例中，NSG 被限制为仅访问 WestCentralUS 和 WestUS2 中的 Azure 存储资源，其中“全部拒绝”规则设为低优先级规则。

![拒绝所有 NSG](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>后续步骤
在本教程中，你创建了一个服务终结点策略并将其关联到了子网。 要详细了解服务终结点策略，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。

