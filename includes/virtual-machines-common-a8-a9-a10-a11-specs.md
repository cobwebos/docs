---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee32886ddb74bdbbe0f240310629c8ef26230a68
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
## <a name="deployment-considerations"></a>部署注意事项
* **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

* **定价和可用性** - 只在标准定价层提供 VM 大小。 有关各 Azure 区域推出的产品，请查看 [按区域提供的产品] (https://azure.microsoft.com/regions/services/)。 
* **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求提高配额，可免费[提出在线客户支持请求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 （默认限制可能会因订阅类别而异。）
  
  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  > 
  > 
* **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，对于许多部署来说，如果需要访问本地资源，则可能至少需要一个基于云的 Azure 虚拟网络或站点到站点连接。 需要时，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。
* **调整大小** - 考虑到专用硬件，可以只对同一大小系列（H 系列或计算密集型 A 系列）内的计算密集型实例进行大小调整。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 此外，不支持从非计算密集型大小调整为计算密集型大小。  

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例
计算密集型实例（H16r、H16mr、A8 和 A9）的子集具有远程直接内存访问 (RDMA) 连接的网络接口。 （使用“r”指定的所选 N 系列大小（如 NC24r）也支持 RDMA 功能。）此接口是对可供其他 VM 大小使用的标准 Azure 网络接口的补充。 
  
此接口允许支持 RDMA 的实例通过 InfiniBand (IB) 网络进行通信，对 H16r、H16mr 和支持 RDMA 的 N 系列虚拟机以 FDR 速率运行，对 A8 和 A9 虚拟机以 QDR 速率运行。 这些 RDMA 功能可以提高某些消息传递接口 (MPI) 应用程序的可伸缩性和性能。

> [!NOTE]
> 在 Azure 中，不支持基于 IB 的 IP。 仅支持 RDMA over IB。
>

在同一个可用性集或 VM 规模集中（使用 Azure 资源管理器部署模型时）或同一个云服务中（使用经典部署模型时）部署支持 RDMA 的 HPC VM。 支持 RDMA 的 HPC VM 可以访问 Azure RDMA 网络的其他要求如下。