---
title: "Azure 容器实例容器组"
description: "了解容器组如何在 Azure 容器实例中工作"
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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5b2abb76fc2bfcf719292db1fdafc8b353d13a3c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---

# <a name="container-groups-in-azure-container-instances"></a>Azure 容器实例中的容器组

Azure 容器实例中的顶层资源是容器组。 本文介绍容器组的定义和它们启用的方案类型。

## <a name="how-a-container-group-works"></a>容器组工作原理

容器组是安排在同一主机上并共享生命周期、本地网络和存储卷的容器集合。 这与 [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 和 [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/) 中“Pod”这一概念相似。

以下关系图显示了一个包含多个容器的容器组示例。

![容器组示例][container-groups-example]

请注意：

- 该组安排在单一主机上。
- 该组公开一个公共 IP 地址（只有一个公开端口）。
- 该组由两个容器组成。 其中一个容器侦听端口 80，另一个容器侦听端口 5000。
- 该组包含两个作为卷装载的 Azure 文件共享，每个容器本地装载一个共享。

### <a name="networking"></a>联网

容器组共享 IP 地址和该 IP 地址上的端口命名空间。 若要启用外部客户端来访问组内的容器，必须从该容器公开 IP 地址上的端口。 因为组内的容器共享端口命名空间，所以不支持端口映射。 组内的容器可以通过它们已公开的端口上的 localhost 相互访问，即使这些端口并未在组的 IP 地址上对外公开。

### <a name="storage"></a>存储

可以指定要在容器组内装载的外部卷。 可以将这些卷映射到组中单个容器内的特定路径。

## <a name="common-scenarios"></a>常见方案

如果希望将单个功能性任务分成少数容器映像，多容器组十分有用，这些映像可以由不同的团队传送并具有单独的资源需求。 使用情况示例包括：

- 应用程序容器和日志记录容器。 日志记录容器通过主要应用程序收集日志和指标输出并将其写入长期存储。
- 应用程序和监视容器。 监视容器定期向应用程序发送请求，确保该应用程序正常运行和响应，并在应用程序出现异常时发出警报。
- 为 Web 应用程序提供服务的容器和从源控件拉取最新内容的容器。

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 资源管理器模板[部署多容器组](container-instances-multi-container-group.md)。

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png
