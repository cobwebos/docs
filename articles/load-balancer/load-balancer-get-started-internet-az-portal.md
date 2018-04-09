---
title: 使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准 | Microsoft Docs
description: 了解如何使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1f1c8d0305334d85500b501aee5a71664bb49050
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准

本文指导逐步通过区域冗余前端使用公共 IP 标准地址创建公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。 默认情况下，标准负载均衡器中的单个前端 IP 地址具有区域冗余性。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
 选择 Azure 资源、区域和 VM 大小系列时可使用可用性区域支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

## <a name="log-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-zone-redundant-load-balancer"></a>创建区域冗余的负载均衡器

1. 从浏览器导航到 Azure 门户：[](http://portal.azure.com)http://portal.azure.com 并使用 Azure 帐户登录。
2. 在屏幕的左上方，选择**创建资源** > **网络** > **负载均衡器**。
3. 在“创建负载均衡器”页面中，在“名称”下，键入 **myLoadBalancer**。
4. 在“类型”下，选择“公共”。
5. 在“SKU”下，选择“标准”。
6. 单击“公用 IP 地址”，单击“新建”，在“创建公用 IP 地址”页面上，在“名称”下键入 **myPublicIPStandard**。
    >[!NOTE] 
    > 默认情况下，在此步骤中创建的公用 IP 是标准 SKU 并且具有区域冗余性。 
8. 在“位置”下，选择“美国东部 2”，然后单击“确定”。 然后，负载均衡器将开始部署，成功完成部署需要几分钟的时间。

    ![使用 Azure 门户创建区域冗余的负载均衡器标准](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>后续步骤
- 详细了解[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。



