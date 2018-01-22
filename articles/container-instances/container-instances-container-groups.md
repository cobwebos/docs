---
title: "Azure 容器实例容器组"
description: "了解容器组如何在 Azure 容器实例中工作"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a42c01917926a4297c97cf9c5dfd1333dbef6793
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure 容器实例中的容器组

Azure 容器实例中的顶层资源是容器组。 本文介绍容器组的定义和它们支持的方案类型。

## <a name="how-a-container-group-works"></a>容器组工作原理

容器组是安排在同一主机上的容器集合。 容器组中的容器共享生命周期、本地网络和存储卷。 这与 [Kubernetes][kubernetes-pod] 和 [DC/OS][dcos-pod] 中“Pod”这一概念相似。

以下关系图显示了一个包含多个容器的容器组示例。

![容器组图][container-groups-example]

此示例容器组：

* 安排在一个主机上。
* 公开一个公共 IP 地址（只有一个公开端口）。
* 由两个容器组成。 其中一个容器侦听端口 80，另一个容器侦听端口 5000。
* 包含两个 Azure 文件共享作为卷装载，每个容器本地装载一个共享。

> [!NOTE]
> 多容器组当前仅限于 Linux 容器。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

### <a name="networking"></a>网络

容器组共享 IP 地址和该 IP 地址上的端口命名空间。 若要启用外部客户端来访问组内的容器，必须从该容器公开 IP 地址上的端口。 因为组内的容器共享端口命名空间，所以不支持端口映射。 组内的容器可以通过它们已公开的端口上的 localhost 相互访问，即使这些端口并未在组的 IP 地址上对外公开。

### <a name="storage"></a>存储

可以指定要在容器组内装载的外部卷。 可以将这些卷映射到组中单个容器内的特定路径。

## <a name="common-scenarios"></a>常见方案

如果希望将单个功能性任务分成少数容器映像，多容器组十分有用，这些映像可以由不同的团队传送并具有单独的资源需求。

使用情况示例包括：

* 应用程序容器和日志记录容器。 日志记录容器通过主要应用程序收集日志和指标输出并将其写入长期存储。
* 应用程序和监视容器。 监视容器定期向应用程序发送请求，确保该应用程序正常运行和响应，并在应用程序出现异常时发出警报。
* 为 Web 应用程序提供服务的容器和从源控件拉取最新内容的容器。

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 资源管理器模板[部署多容器组](container-instances-multi-container-group.md)。

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
