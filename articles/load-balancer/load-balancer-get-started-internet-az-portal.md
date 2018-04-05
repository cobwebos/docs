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
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准

本文指导逐步通过区域冗余前端使用公共 IP 标准地址创建公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-for-availability-zones-preview"></a>注册可用性区域预览
 
可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

## <a name="log-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-zone-redundant-load-balancer"></a>创建区域冗余的负载均衡器

1. 从浏览器导航到 Azure 门户：[](http://portal.azure.com)http://portal.azure.com 并使用 Azure 帐户登录。
2. 在屏幕的左上方，选择**创建资源** > **网络** > **负载均衡器**。
3. 在“创建负载均衡器”中的“名称”下，键入 **myPublicLB**。
4. 在“类型”下，选择“公共”。
5. 在“SKU”下，选择“标准(预览)”。
6. 依次单击“公共 IP 地址”、“新建”，在“创建公共 IP 地址”页上，在“名称”下键入 **myPublicIPStandard**，对于“可用性区域(预览)”，选择“区域冗余”。
7. 在“位置”下，选择“美国东部 2”，然后单击“确定”。 然后，负载均衡器将开始部署，成功完成部署需要几分钟的时间。

    ![使用 Azure 门户创建区域冗余的负载均衡器标准](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>后续步骤
- 了解如何[在可用性区域中创建公共 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



