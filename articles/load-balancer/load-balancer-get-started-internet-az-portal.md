---
title: "使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准 | Microsoft Docs"
description: "了解如何使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>使用 Azure 门户通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准

本文指导逐步通过区域冗余前端使用公共 IP 标准地址创建公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>注册可用性区域、负载均衡器标准和公共 IP 标准预览
 
>[!NOTE]
[负载均衡器标准 SKU](https://aka.ms/azureloadbalancerstandard) 目前处于预览状态。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请将正式版[负载均衡器基本 SKU](load-balancer-overview.md) 用于生产服务。 

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

为负载均衡器的前端公共 IP 地址选择区域或区域冗余选项之前，必须先完成[注册可用性区域预览版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步骤。

## <a name="log-in-to-azure"></a>登录 Azure 

登录 Azure 门户 ( https://portal.azure.com )。

## <a name="create-a-zone-redundant-load-balancer"></a>创建区域冗余的负载均衡器

1. 从浏览器导航到 Azure 门户：[http://portal.azure.com](http://portal.azure.com)，并使用 Azure 帐户登录。
2. 在屏幕的左上方，选择“新建” > “网络” > “负载均衡器”。
3. 在“创建负载均衡器”中的“名称”下，键入 **myPublicLB**。
4. 在“类型”下，选择“公共”。
5. 在“SKU”下，选择“标准(预览)”。
6. 依次单击“公共 IP 地址”、“新建”，在“创建公共 IP 地址”页上，在“名称”下键入 **myPublicIPStandard**，对于“可用性区域(预览)”，选择“区域冗余”。
7. 在“位置”下，选择“美国东部 2”，然后单击“确定”。 然后，负载均衡器将开始部署，成功完成部署需要几分钟的时间。

    ![使用 Azure 门户创建区域冗余的负载均衡器标准](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>后续步骤
- 了解如何[在可用性区域中创建公共 IP](../virtual-network/create-public-ip-availability-zone-portal.md)



