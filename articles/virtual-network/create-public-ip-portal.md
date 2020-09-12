---
title: 创建公共 IP-Azure 门户
description: 了解如何在 Azure 门户中创建公共 IP
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 49a89ee90d28c9c7a3f59424b773ee0f221381e4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89301626"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共 IP 地址

本文介绍如何使用 Azure 门户创建公共 IP 地址资源。 有关此可以关联到哪些资源的详细信息，基本 SKU 和标准 SKU 之间的差异，以及其他相关信息，请参阅 [公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)。  对于此示例，我们将仅重点介绍 IPv4 地址;有关 IPv6 地址的详细信息，请参阅 [适用于 Azure VNet 的 IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)。

# <a name="standard-sku---using-zones"></a>[**标准 SKU-使用区域**](#tab/option-create-public-ip-standard-zones)

使用以下步骤创建名为 **myStandardZRPublicIP**的标准区域冗余公共 IP 地址。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在 " **创建公共 IP 地址** " 页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择 IPv4                 |    
    | SKU                     | 选择“标准”         |
    | 名称                    | 输入 *myStandardZRPublicIP*          |
    | IP 地址分配   | 请注意，这将被锁定为 "静态"                                        |
    | 空闲超时 (分钟)   | 将值保留为4        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择 " **新建** "，输入 myResourceGroup，然后选择 **"确定"** |
    | 位置                | 选择 **美国东部 2**      |
    | 可用性区域       | 选择 " **区域冗余** " 或 "选择特定区域" (参阅下面的注释)  |

请注意，这些只是包含 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)的区域中的有效选择。   (你还可以在这些区域中选择特定的区域，但不能恢复范围故障。 ) 

# <a name="standard-sku---no-zones"></a>[**标准 SKU-无区域**](#tab/option-create-public-ip-standard)

使用以下步骤创建一个标准公共 IP 地址，作为名为 **myStandardPublicIP**的非区域性资源。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在 " **创建公共 IP 地址** " 页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择 IPv4                 |    
    | SKU                     | 选择“标准”         |
    | 名称                    | 输入 *myStandardPublicIP*          |
    | IP 地址分配   | 请注意，这将被锁定为 "静态"                                        |
    | 空闲超时 (分钟)   | 将值保留为4        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择 " **新建** "，输入 myResourceGroup，然后选择 **"确定"** |
    | 位置                | 选择 **美国东部 2**      |
    | 可用性区域       | 选择 " **不 (区域** "，并参阅下面的注释)  |

此选择在所有区域中有效，并且是区域中标准公共 IP 地址的默认选择，无 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用以下步骤创建名为 **myBasicPublicIP**的基本静态公共 IP 地址。  基本公共 Ip 没有可用性区域的概念。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在 " **创建公共 IP 地址** " 页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择 IPv4                 |    
    | SKU                     | 选择“标准”         |
    | 名称                    | 输入 *myBasicPublicIP*          |
    | IP 地址分配   | 选择 **静态** (请参阅下面的注释)                                      |
    | 空闲超时 (分钟)   | 将值保留为4        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择 " **新建** "，输入 myResourceGroup，然后选择 **"确定"** |
    | 位置                | 选择 **美国东部 2**      |

如果 IP 地址可在一段时间内更改，则可以选择 **动态** ip 分配。

---

## <a name="additional-information"></a>其他信息 

有关上面所列各个字段的详细信息，请参阅 [管理公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- 将 [公共 IP 地址关联到虚拟机](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。