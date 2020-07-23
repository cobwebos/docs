---
title: Azure Integration Runtime IP 地址
description: 了解必须允许来自哪些 IP 地址的入站流量，以便正确配置防火墙来保护对数据存储的网络访问。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 0c64a38295754e4754326dec126bfbc36e1bef61
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523326"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 地址

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 集成运行时使用的 IP 地址取决于 Azure 集成运行时所在的区域。 同一区域中的所有 Azure 集成运行时都使用相同的 IP 地址范围。

> [!IMPORTANT]  
> 启用托管虚拟网络的数据流和 Azure Integration Runtime 不支持使用固定 IP 范围。
>
> 可以将这些 IP 范围用于数据移动、管道和外部活动执行。 这些 IP 范围可用于数据存储/网络安全组 (NSG)/防火墙中的允许列表，以便通过 Azure 集成运行时进行入站访问。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure 集成运行时 IP 地址：特定的区域

对于资源所在的特定 Azure 区域中的 Azure Integration Runtime ，允许来自为其列出的 IP 地址的流量。 可以从[服务标记 IP 范围下载链接](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)中获取服务标记的 IP 范围列表。 例如，如果 Azure 区域为**AustraliaEast**，则可以从 DATAFACTORY 获取 IP 范围列表 **。**


## <a name="known-issue-with-azure-storage"></a>Azure 存储的已知问题

* 当连接到 Azure 存储帐户时，IP 网络规则对从存储帐户所在区域的 Azure 集成运行时发出请求不起作用。 有关更多详细信息，请[参阅此文](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)。 

  因此，我们建议[在连接到 Azure 存储时改用受信任的服务](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)。 

## <a name="next-steps"></a>后续步骤

* [Azure 数据工厂中数据移动的安全注意事项](data-movement-security-considerations.md)
