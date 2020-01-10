---
title: 容器组简介
description: 了解 Azure 容器实例中的容器组，它是共享生命周期和资源（如存储和网络）的实例集合。
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 19fa50f83a2593b8914931e25fa99cb2e4896227
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770265"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure 容器实例中的容器组

Azure 容器实例中的顶层资源是容器组。 本文介绍容器组的定义和它们支持的方案类型。

## <a name="what-is-a-container-group"></a>什么是容器组？

容器组是安排在同一主机上的容器集合。 容器组中的容器共享生命周期、资源、本地网络和存储卷。 它在概念上类似于[Kubernetes][kubernetes-pod]中的*pod* 。

以下关系图显示了一个包含多个容器的容器组示例：

![容器组图][container-groups-example]

此示例容器组：

* 安排在一个主机上。
* 已分配 DNS 名称标签。
* 公开一个公共 IP 地址（只有一个公开端口）。
* 由两个容器组成。 其中一个容器侦听端口 80，另一个容器侦听端口 5000。
* 包含两个 Azure 文件共享作为卷装载，每个容器本地装载一个共享。

> [!NOTE]
> 多容器组目前仅支持 Linux 容器。 对于 Windows 容器，Azure 容器实例仅支持单个实例的部署。 尽管我们正在努力将所有功能引入 Windows 容器，但你可以在服务[概述](container-instances-overview.md#linux-and-windows-containers)中找到当前的平台差异。

## <a name="deployment"></a>部署

下面是部署多容器组的两种常见方法：使用[资源管理器模板][resource-manager template]或[YAML 文件][yaml-file]。 部署容器实例时，如果需要部署其他 Azure 服务资源（例如[Azure 文件共享][azure-files]），则建议使用资源管理器模板。 由于 YAML 格式更简洁，因此当部署仅包含容器实例时，建议使用 YAML 文件。 有关可以设置的属性的详细信息，请参阅[资源管理器模板参考](/azure/templates/microsoft.containerinstance/containergroups)或[YAML 参考](container-instances-reference-yaml.md)文档。

若要保留容器组的配置，可以使用 Azure CLI 命令[az 容器 export][az-container-export]将配置导出到 YAML 文件。 通过导出，你可以在版本控制中将容器组配置存储为 "配置为代码"。 还可以将导出的文件用作使用 YAML 开发新配置时的起点。



## <a name="resource-allocation"></a>资源分配

Azure 容器实例通过在组中添加实例的[资源请求][resource-requests]，将 cpu、内存和可选[gpu][gpus] （预览版）等资源分配给多容器组。 取 CPU 资源示例：如果创建包含两个实例的容器组，每个实例请求1个 CPU，则会为该容器组分配2个 cpu。

### <a name="resource-usage-by-instances"></a>按实例的资源使用情况

组中的每个容器实例都分配了其资源请求中指定的资源。 但是，如果配置其可选的[资源限制][resource-limits]属性，则组中的实例所使用的最大资源可能会不同。 实例的资源限制必须大于或等于必需的[资源请求][resource-requests]属性。

* 如果未指定资源限制，则实例的最大资源使用率与资源请求相同。

* 如果为实例指定限制，则实例的最大使用量可能大于请求，直至达到所设置的限制。 同样，组中其他实例的资源使用可能会降低。 可为实例设置的最大资源限制是分配给组的总资源数。
    
例如，在包含两个实例的组中，每个请求1个 CPU，其中一个容器可能运行的工作负荷需要更多的 Cpu 才能运行。

在这种情况下，可以为实例设置2个 Cpu 的资源限制。 此配置允许容器使用最多满2个 Cpu （如果可用）。

### <a name="minimum-and-maximum-allocation"></a>最小和最大分配

* 向容器组分配**至少**1 个 CPU 和 1 GB 的内存。 可以为组中的各个容器实例预配少于1个 CPU 和 1 GB 的内存。 

* 有关容器组中的**最大**资源，请参阅部署区域中 Azure 容器实例的[资源可用性][region-availability]。

## <a name="networking"></a>联网

容器组可以共享一个面向外部的 IP 地址、该 IP 地址上的一个或多个端口，以及一个具有完全限定的域名（FQDN）的 DNS 标签。 若要启用外部客户端来访问组内的容器，必须从该容器公开 IP 地址上的端口。 由于组中的容器共享端口命名空间，因此不支持端口映射。 删除容器组后，将释放容器组的 IP 地址和 FQDN。 

在容器组内，容器实例可以通过任意端口上的 localhost 互相连接，即使这些端口未在组的 IP 地址或容器中对外公开。

（可选）将容器组部署到[Azure 虚拟网络][virtual-network]（预览版）中，以允许容器与虚拟网络中的其他资源安全通信。

## <a name="storage"></a>存储空间

可以指定要在容器组内装载的外部卷。 支持的卷包括：
* [Azure 文件共享][azure-files]
* [机密][secret]
* [空目录][empty-directory]
* [克隆的 git 存储库][volume-gitrepo]

可以将这些卷映射到组中单个容器内的特定路径。 

## <a name="common-scenarios"></a>常见方案

如果希望将单个功能性任务分成少数容器映像，则多容器组十分有用。 然后，这些映像可以由不同的团队交付并具有单独的资源需求。

使用情况示例包括：

* 为 Web 应用程序提供服务的容器和从源控件拉取最新内容的容器。
* 应用程序容器和日志记录容器。 日志记录容器收集主要应用程序输出的日志和指标并将其写入长期存储。
* 应用程序容器和监视容器。 监视容器定期向应用程序发送请求，确保该应用程序正常运行和响应，并在应用程序出现异常时发出警报。
* 前端容器和后端容器。 前端可以为 web 应用程序提供服务，后端运行服务来检索数据。 

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 资源管理器模板部署多容器容器组：

> [!div class="nextstepaction"]
> [部署容器组][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
