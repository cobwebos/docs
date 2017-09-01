---
title: "Azure 容器实例概述 | Azure Docs"
description: "了解 Azure 容器实例"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: b77ba307938b6b1f5221de40cc135d17c15c1d9b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---

# <a name="azure-container-instances"></a>Azure 容器实例

容器正很快成为对云应用程序执行打包、部署和管理操作的首选方式。 Azure 容器实例提供了在 Azure 中运行容器的最简捷方式，既无需预配任何虚拟机，也不必采用更高级的服务。

不管什么方案（包括简单应用程序、任务自动化、生成作业），只要能够在隔离容器中操作，就可以使用 Azure 容器实例这种解决方案。 对于需要完整容器业务流程的方案（包括跨多个容器的服务发现、自动缩放、协调式应用程序升级），建议使用 [Azure 容器服务](https://docs.microsoft.com/azure/container-service/)。

## <a name="fast-startup-times"></a>启动时间短

与虚拟机相比，容器的启动优势明显。 使用 Azure 容器实例时，只需数秒即可在 Azure 中启动容器，不需预配和管理 VM。

## <a name="hypervisor-level-security"></a>虚拟机监控程序级别的安全性

从历史上看，容器提供了应用程序依赖项隔离和资源调控功能，但不能认为其功能已强大到可以进行恶意的多租户使用。 使用 Azure 容器实例时，应用程序在容器中的隔离与在 VM 中相同。

## <a name="custom-sizes"></a>自定义大小

容器通常优化成只运行单个应用程序，但此类应用程序的具体需求可能差异很大。 使用 Azure 容器实例时，可以完全根据需要来请求 CPU 核心数和内存。 费用取决于具体请求，按秒计算，因此可以根据需求来严格控制花费。

## <a name="public-ip-connectivity"></a>公共 IP 连接

使用 Azure 容器实例时，可以通过公共 IP 地址将容器直接向 Internet 公开。 在将来，我们会扩展网络功能，包括允许集成虚拟网络、负载均衡器以及 Azure 网络基础结构的其他核心部分。

## <a name="persistent-storage"></a>持久性存储

为了通过 Azure 容器实例来检索和持久保存状态，我们提供直接装载 Azure 文件共享的功能。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

使用 Azure 容器实例时，可以通过同一 API 来计划 Windows 和 Linux 容器。 指出基础 OS 类型即可，其余设置都是相同的。

## <a name="co-scheduled-groups"></a>共同计划组

Azure 容器实例支持对共享主机、本地网络、存储和生命周期的多容器组进行计划。 这样即可将主应用程序与其他充当配角（例如进行日志记录）的应用程序组合使用。

## <a name="next-steps"></a>后续步骤

尝试按照[快速入门指南](container-instances-quickstart.md)，使用单个命令将容器部署到 Azure。
