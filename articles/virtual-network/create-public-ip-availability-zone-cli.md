---
title: "使用 Azure CLI 创建分区的公共 IP 地址 | Microsoft Docs"
description: "使用 Azure CLI 在可用性区域中创建公共 IP。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>使用 Azure CLI 在可用性区域中创建公共 IP 地址

可以在 Azure 可用性区域（预览）中部署公共 IP 地址。 可用性区域是 Azure 区域中物理上独立的区域。 了解如何：

> * 在可用性区域中创建公共 IP 地址
> * 标识在可用性区域中创建的相关资源

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本教程要求运行高于 2.0.17 的 Azure CLI 版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md)（创建 Azure 支持票证）。

## <a name="create-a-zonal-public-ip-address"></a>创建分区的公共 IP 地址

使用以下命令在可用性区域中创建公共 IP 地址：


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](security-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。

## <a name="get-zone-information-about-a-public-ip-address"></a>获取有关公共 IP 地址的区域信息

使用以下命令获取公共 IP 地址的区域信息：

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>后续步骤

- 了解有关[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)的详细信息
- 了解有关[公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)的详细信息 
