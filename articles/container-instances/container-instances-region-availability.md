---
title: 按区域的资源可用性
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: aef66a9fdbe73ccd4da79ce972b7beb061e9fe35
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533470"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文详述 Azure 容器实例的计算和内存资源在 Azure 区域的可用性。 

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。 

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 若要减少此类部署失败，请尝试部署具有较低资源设置的实例，或稍后尝试部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 常规

以下区域和资源可供包含 Linux 和[受支持的](container-instances-faq.md#what-windows-base-os-images-are-supported)基于 Windows Server 2016 的容器的容器组使用。

| 位置 | 操作系统 | CPU | 内存 (GB) |
| -------- | -- | :---: | :-----------: |
| 加拿大中部、印度中部、美国中部、东亚、美国东部、美国东部 2、北欧、美国中南部、东南亚、英国南部、美国西部 | Linux | 4 | 16 |
| 西欧、美国西部 2 | Linux | 4 | 14 |
| 澳大利亚东部、日本东部 | Linux | 2 | 8 |
| 美国中北部、印度南部 | Linux | 2 | 3.5 |
| 西欧 | Windows | 4 | 16 |
| 美国东部、美国西部 | Windows | 4 | 14 |
| 澳大利亚东部、加拿大中部、印度中部、美国中部、东亚、美国东部 2、日本东部、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、美国西部 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC，1809 部署（预览版）

以下区域和资源可供包含基于 Windows Server 2019 的容器（预览版）的容器组使用。

| 位置 | 操作系统 | CPU | 内存 (GB) |
| -------- | -- | :---: | :-----------: |
| 东南亚、北欧、西欧、美国中部、美国东部、美国西部、美国西部 2 | Windows | 4 | 16 |
| 美国东部 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>可用性 - 虚拟网络部署（预览版）

以下区域和资源可供部署在 [Azure 虚拟网络](container-instances-vnet.md)（预览版）中的容器组使用。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU 资源（预览版）

以下区域和资源可供与 [GPU 资源](container-instances-gpu.md)（预览版）一起部署的容器组使用。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>后续步骤

如果希望看到更多的区域，或者希望提高资源可用性，请通过 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知我们的团队。

有关容器实例部署故障排除的信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。
