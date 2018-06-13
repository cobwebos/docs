---
title: Azure 容器实例概述
description: 了解 Azure 容器实例
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2da61d52e43857237f6e231c03ea62ba5eefdadd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767965"
---
# <a name="azure-container-instances"></a>Azure 容器实例

容器正在变成对云应用程序执行打包、部署和管理操作的首选方式。 Azure 容器实例提供了在 Azure 中运行容器的最简捷方式，既无需管理任何虚拟机，也不必采用更高级的服务。

不管什么方案（包括简单应用程序、任务自动化、生成作业），只要能够在隔离容器中操作，就可以使用 Azure 容器实例这种解决方案。 对于需要完整容器业务流程的方案（包括跨多个容器的服务发现、自动缩放、协调式应用程序升级），建议使用 [Azure Kubernetes Service (AKS)](../aks/index.yml)。

## <a name="fast-startup-times"></a>启动时间短

与虚拟机相比，容器的启动优势明显。 Azure 容器实例可在数秒内启动 Azure 中的容器，且无需预配和管理 VM。

## <a name="public-ip-connectivity-and-dns-name"></a>公共 IP 连接和 DNS 名称

使用 Azure 容器实例可以通过公共 IP 地址和完全限定的域名 (FQDN) 直接向 Internet 公开容器。 创建容器实例时，可以指定自定义的 DNS 名称标签，以便应用程序可在 *customlabel*.*azureregion*.azurecontainer.io 上访问。

## <a name="hypervisor-level-security"></a>虚拟机监控程序级别的安全性

从历史上看，容器提供了应用程序依赖项隔离和资源调控功能，但不能认为其功能已强大到可以进行恶意的多租户使用。 Azure 容器实例保证容器中的应用程序像在 VM 中一样保持隔离状态。

## <a name="custom-sizes"></a>自定义大小

容器通常优化成只运行单个应用程序，但此类应用程序的具体需求可能差异很大。 Azure 容器实例允许确切地指定 CPU 核心数和内存量，因此可提供最佳的利用方式。 费用取决于具体请求并按秒计收，因此可以根据实际需求来严格控制花费。

## <a name="persistent-storage"></a>持久性存储

为了通过 Azure 容器实例来检索和持久保存状态，我们提供直接[装载 Azure 文件共享](container-instances-mounting-azure-files-volume.md)的功能。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

Azure 容器实例可以使用同一 API 来计划 Windows 和 Linux 容器。 直接在创建[容器组](container-instances-container-groups.md)时指定 OS 类型。

某些功能当前仅限于 Linux 容器。 我们正致力于为 Windows 容器提供功能奇偶一致性。可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="co-scheduled-groups"></a>共同计划组

Azure 容器实例支持对共享主机、本地网络、存储和生命周期的[多容器组](container-instances-container-groups.md)进行计划。 这样即可将主要应用程序容器与其他配角容器（例如日志记录分支）结合使用。

## <a name="next-steps"></a>后续步骤

尝试按照快速入门指南，使用单个命令将容器部署到 Azure：

> [!div class="nextstepaction"]
> [Azure 容器实例快速入门](container-instances-quickstart.md)
