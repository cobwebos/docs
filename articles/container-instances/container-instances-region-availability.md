---
title: Azure 容器实例资源可用性
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554835"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文详述 Azure 容器实例的计算和内存资源在 Azure 区域的可用性。 

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。 有关最新信息，请使用[列表功能](/rest/api/container-instances/listcapabilities/listcapabilities) API。 

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 若要减少此类部署失败，请尝试部署具有较低资源设置的实例，或稍后尝试部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 常规

| 位置 | 操作系统 | CPU | 内存 (GB) |
| -------- | -- | :---: | :-----------: |
| 加拿大中部、美国中部、美国东部 2、美国中南部 | Linux | 4 | 16 |
| 美国东部、北欧、西欧、美国西部、美国西部 2 | Linux | 4 | 14 |
| 日本东部 | Linux | 2 | 8 |
| 澳大利亚东部、东南亚 | Linux | 2 | 7 |
| 印度中部、东亚、美国中北部、印度南部 | Linux | 2 | 3.5 |
| 美国东部、西欧、美国西部 | Windows | 4 | 14 |
| 澳大利亚东部、加拿大中部、印度中部、美国中部、东亚、美国东部 2、日本东部、美国中北部、北欧、美国中南部、印度南部、东南亚、美国西部 2 | Windows | 2 | 3.5 |

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
