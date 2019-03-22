---
title: 创建具有区域前端的负载均衡器 - Azure 门户
titlesuffix: Azure Load Balancer
description: 了解如何使用 Azure 门户创建带有区域前端的标准负载均衡器
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c81ff5ea330c4c0ba26a92a3b5399cfa961e4b2b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856852"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>使用 Azure 门户创建具有区域性前端的标准负载均衡器

本文分步指导你创建具有区域性前端 IP 配置的公用[标准负载均衡器](https://aka.ms/azureloadbalancerstandard)。 若要了解可用性区域如何与标准负载均衡器配合工作，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 选择 Azure 资源、区域和 VM 大小系列时可使用可用性区域支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

## <a name="log-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>创建具有区域性前端 IP 地址的负载均衡器

1. 从浏览器导航到 Azure 门户：[https://portal.azure.com](https://portal.azure.com) 并使用 Azure 帐户登录。
2. 在屏幕的左上方，选择**创建资源** > **网络** > **负载均衡器**。
3. 在“创建负载均衡器”页面中，在“名称”下，键入 **myLoadBalancer**。
4. 在“类型”下，选择“公共”。
5. 在“SKU”下，选择“标准”。
6. 依次单击“选择公用 IP 地址”、“新建”，在“创建公用 IP 地址”页上，在“名称”下键入**myPublicIPZonal**，对于 SKU，选择“标准”，对于可用性区域，选择 **1**。
    
>[!NOTE] 
> 默认情况下，在此步骤中创建的公用 IP 是标准 SKU。

1. 对于“资源组”，单击“新建”，然后键入 **myResourceGroupZLB** 作为资源组的名称。
1. 对于“位置”，选择“西欧”，然后单击“确定”。 然后，负载均衡器将开始部署，成功完成部署需要几分钟的时间。

    ![使用 Azure 门户创建区域冗余的标准负载均衡器](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>后续步骤
- 详细了解[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。



