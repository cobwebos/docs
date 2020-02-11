---
title: 按区域的资源可用性
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
ms.topic: article
ms.date: 01/31/2020
ms.author: danlep
ms.openlocfilehash: 3f64a13331658c1360c9d54f6f57a68c2c91fc6f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117822"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文详细介绍了 azure 区域中的 Azure 容器实例计算、内存和存储资源的可用性以及目标操作系统。 

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。 

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 若要减少此类部署失败，请尝试部署具有较低资源设置的实例，或稍后尝试部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 常规

以下区域和资源可供包含 Linux 和[受支持的](container-instances-faq.md#what-windows-base-os-images-are-supported)基于 Windows Server 2016 的容器的容器组使用。

| 区域 | OS | 最大 CPU | 最大内存（GB） | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 巴西南部、加拿大中部、印度中部、美国中部、东亚、美国东部、美国东部2、北欧、美国中南部、东南亚、印度、英国南部、西欧、美国西部、美国西部2 | Linux | 4 | 16 | 50 |
| 澳大利亚东部、日本东部 | Linux | 2 | 8 | 50 |
| 美国中北部 | Linux | 2 | 3.5 | 50 |
| 巴西南部、日本东部、西欧 | Windows | 4 | 16 | 20 |
| 美国东部、美国西部 | Windows | 4 | 14 | 20 |
| 澳大利亚东部、加拿大中部、印度中部、美国中部、东亚、美国东部2、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、美国西部2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC，1809 部署（预览版）

以下区域和资源可供包含基于 Windows Server 2019 的容器（预览版）的容器组使用。

| 区域 | OS | 最大 CPU | 最大内存（GB） | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 澳大利亚东部、巴西南部、加拿大中部、印度中部、美国中部、东亚、美国东部、日本东部、美国中北部、北欧、美国中南部、东南亚、印度、英国南部、西欧 | Windows | 4 | 16 | 20 |
| 美国东部2、美国西部2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>可用性-虚拟网络部署

以下区域和资源可用于在[Azure 虚拟网络](container-instances-vnet.md)中部署的容器组。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU 资源（预览版）

以下区域和资源可供与 [GPU 资源](container-instances-gpu.md)（预览版）一起部署的容器组使用。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>后续步骤

如果希望看到更多的区域，或者希望提高资源可用性，请通过 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知我们的团队。

有关容器实例部署故障排除的信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。
