---
title: 资源可用性（按区域）
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: c84ab7833f7c90d5d4b3c340c268fd9f75e20da9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399402"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文详细介绍了 Azure 区域和目标操作系统中 Azure 容器实例计算、内存和存储资源的可用性。 

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。 

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 要缓解此类部署失败，请尝试使用较低的资源设置部署实例，或稍后尝试部署，或在具有可用资源的不同区域中尝试部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 常规

以下区域和最大资源可用于具有 Linux[和支持的](container-instances-faq.md#what-windows-base-os-images-are-supported)基于 Windows Server 2016 的容器组。

| 区域 | OS | 最大 CPU | 最大内存 (GB) | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 巴西南部、加拿大中部、印度中部、美国中部、东亚、美国东部、美国东部 2、北欧、美国中南部、东南亚、南印度、英国南部、西欧、美国西部、美国西部 2 | Linux | 4 | 16 | 50 |
| 澳大利亚东部、日本东部 | Linux | 2 | 8 | 50 |
| 美国中北部 | Linux | 2 | 3.5 | 50 |
| 巴西 南部、日本东部、西欧 | Windows | 4 | 16 | 20 |
| 美国东部、美国西部 | Windows | 4 | 14 | 20 |
| 澳大利亚东部、加拿大中部、印度中部、美国中部、东亚、美国东部 2、美国中北部、北欧、美国中南部、东南亚、南印度、英国南部、美国西部 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC，1809 部署（预览版）

以下区域和最大资源可用于基于 Windows Server 2019 的容器组（预览）。

| 区域 | OS | 最大 CPU | 最大内存 (GB) | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 澳大利亚东部、巴西南部、加拿大中部、印度中部、美国中部、东亚、美国东部、日本东部、美国中北部、北欧、美国中南部、东南亚、南印度、英国南部、西欧 | Windows | 4 | 16 | 20 |
| 美国东部 2， 西美国 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>可用性 - 虚拟网络部署

部署在[Azure 虚拟网络中](container-instances-vnet.md)的容器组可以使用以下区域和最大资源。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU 资源（预览版）

使用[GPU 资源](container-instances-gpu.md)部署的容器组可以使用以下区域和最大资源（预览）。

> [!IMPORTANT]
> GPU 资源仅应要求可用。 要请求访问 GPU 资源，请提交[Azure 支持请求][azure-support]。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>后续步骤

如果希望看到更多的区域，或者希望提高资源可用性，请通过 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知我们的团队。

有关容器实例部署故障排除的信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
