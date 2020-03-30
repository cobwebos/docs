---
title: 创建并关联服务终结点策略 - Azure 门户
titlesuffix: Azure Virtual Network
description: 在本文中了解如何在 Azure 门户中设置和关联服务终结点策略。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651129"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>使用 Azure 门户创建、更改或删除服务终结点策略

借助服务终结点策略，可越过服务终结点筛选虚拟网络流量，将范围限制到特定的 Azure 资源。 如果不熟悉服务终结点策略，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)了解详细信息。

 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建服务终结点策略
> * 创建服务终结点策略定义
> * 创建带子网的虚拟网络
> * 将服务终结点策略关联到子网中

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-service-endpoint-policy"></a>创建服务终结点策略

1. 选择 Azure 门户左上角的“+ 创建资源”****。
2. 在搜索窗格中，键入"服务终结点策略"，然后选择 **"服务终结点策略**"，然后选择"**创建**"。

![创建服务终结点策略](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. 在“基本信息”中，输入或选择以下信息**** 

   - 订阅：为策略选择订阅
   - 资源组：选择 **"创建新"** 并输入*我的资源组*
   - 名称：myEndpointPolicy
   - 地点 ： 美国中部
 
   ![“创建服务终结点策略”基本信息](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. 选择 **= 在****"资源**"下添加，并在 **"添加资源**窗格"中输入或选择以下信息

   - 服务 ：只有**Microsoft.存储**可用于服务终结点策略
   - 范围：从**单一帐户**中选择一个，**订阅中的所有帐户****和资源组中的所有帐户**
   - 订阅：选择用于存储帐户的订阅。 策略和存储帐户可位于不同的订阅。
   - 资源组：选择你的资源组。 如果范围设置为“资源组中的所有帐户”或“单个帐户”，则必填。  
   - 资源：在选定的订阅或资源组下选择 Azure 存储资源
   - 单击底部的 **"添加"** 按钮以完成资源添加

   ![服务终结点策略定义 - 资源](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - 根据需要重复上述步骤添加更多资源

5. 可选：在**标记**中输入或选择以下信息：
   
   - 密钥：选择策略的键。 例如 Dept     
   - 值：输入该键的值对。 例如 Finance

6. 选择 **"审阅 " 创建**。 验证信息并单击“创建”****。 要做进一步的编辑，请单击“上一步”****。 

   ![“创建服务终结点策略”最终验证](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>查看服务终结点策略 

1. 在门户的“所有服务”框中，开始键入“服务终结点策略”****。 选择**服务终结点策略**。
2. 在“订阅”下，选择你的订阅和资源组，如下图中所示****

   ![显示策略](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. 选择策略，再单击“策略定义”以查看或添加更多策略定义****。

   ![显示策略定义](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. 选择“关联的子网”以查看与策略关联的子网****。 如果尚未关联任何子网，请按照下一步中的说明操作。

   ![关联的子网](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. 将策略关联到子网

>[!WARNING] 
> 在将策略关联到给定的子网之前，请确保从子网访问的所有资源都添加到策略定义中。 策略关联后，仅允许通过服务终结点访问*允许列出的*资源。 
>
> 还确保与服务终结点策略关联的子网中不存在托管 Azure 服务

- 必须先创建虚拟网络和子网，然后才能将策略关联到子网。 请参阅[创建虚拟网络](./quick-create-portal.md)文章，以寻求帮助。

- 设置虚拟网络和子网后，需要为 Azure 存储配置虚拟网络服务终结点。 在虚拟网络边栏选项卡上，选择**服务终结点**，并在下一个窗格中选择**Microsoft.Storage，** 并在**子网**下选择所需的 VNet 或子网

- 现在，如果已在为子网配置服务终结点之前已创建了服务终结点策略，则可以选择从上述窗格的下拉列表中选择服务终结点策略，如下所示

    ![创建服务终结点时关联子网](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- 或者，如果您在配置了服务终结点后关联服务终结点策略，则可以选择通过导航到 **"关联子网"** 窗格来从服务终结点策略边栏选项卡中关联子网，如下所示

    ![通过 SEP 关联子网](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>根据此子网的服务终结点策略，将限制对所有区域中的 Azure 存储资源的访问。

## <a name="next-steps"></a>后续步骤
在本教程中，你创建了一个服务终结点策略并将其关联到了子网。 要详细了解服务终结点策略，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。
