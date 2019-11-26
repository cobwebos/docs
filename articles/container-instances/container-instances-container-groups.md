---
title: Introduction to container groups
description: Learn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 9fbf9fea7da0896ee6c0e248d18e18d52798fbd7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482116"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure 容器实例中的容器组

Azure 容器实例中的顶层资源是容器组。 本文介绍容器组的定义和它们支持的方案类型。

## <a name="what-is-a-container-group"></a>What is a container group?

容器组是安排在同一主机上的容器集合。 The containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a *pod* in [Kubernetes][kubernetes-pod].

以下关系图显示了一个包含多个容器的容器组示例：

![容器组图][container-groups-example]

此示例容器组：

* 安排在一个主机上。
* 已分配 DNS 名称标签。
* 公开一个公共 IP 地址（只有一个公开端口）。
* 由两个容器组成。 其中一个容器侦听端口 80，另一个容器侦听端口 5000。
* 包含两个 Azure 文件共享作为卷装载，每个容器本地装载一个共享。

> [!NOTE]
> Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance. While we are working to bring all features to Windows containers, you can find current platform differences in the service [Overview](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>部署

Here are two common ways to deploy a multi-container group: use a [Resource Manager template][resource-manager template] or a [YAML file][yaml-file]. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an [Azure Files share][azure-files]) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances. For details on properties you can set, see the [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) or [YAML reference](container-instances-reference-yaml.md) documentation.

To preserve a container group's configuration, you can export the configuration to a YAML file by using the Azure CLI command [az container export][az-container-export]. Export allows you to store your container group configurations in version control for "configuration as code." 还可以将导出的文件用作使用 YAML 开发新配置时的起点。



## <a name="resource-allocation"></a>Resource allocation

Azure Container Instances allocates resources such as CPUs, memory, and optionally [GPUs][gpus] (preview) to a container group by adding the [resource requests][resource-requests] of the instances in the group. Taking CPU resources as an example, if you create a container group with two instances, each requesting 1 CPU, then the container group is allocated 2 CPUs.

### <a name="resource-usage-by-instances"></a>Resource usage by instances

Each container instance is allocated the resources specified in its resource request. However, the resource usage by a container instance in a group depends on how you configure its optional [resource limit][resource-limits] property.

* If you don't specify a resource limit, the instance's maximum resource usage is the same as its resource request.

* If you specify a resource limit for an instance, you can adjust the instance's resource usage for its workload, either reducing or increasing usage relative to the resource request. The maximum resource limit you can set is the total resources allocated to the group.
    
    For example, in a group with two instances requesting 1 CPU, one of your containers might run a workload that requires more CPUs to run than the other.

    In this scenario, you could set a resource limit of 0.5 CPU for one instance, and a limit of 2 CPUs for the second. This configuration limits the first container's resource usage to 0.5 CPU, allowing the second container to use up to the full 2 CPUs if available.

For more information, see the [ResourceRequirements][resource-requirements] property in the container groups REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimum and maximum allocation

* Allocate a **minimum** of 1 CPU and 1 GB of memory to a container group. Individual container instances within a group can be provisioned with less than 1 CPU and 1 GB of memory. 

* For the **maximum** resources in a container group, see the [resource availability][region-availability] for Azure Container Instances in the deployment region.

## <a name="networking"></a>网络

容器组共享 IP 地址和该 IP 地址上的端口命名空间。 若要启用外部客户端来访问组内的容器，必须从该容器公开 IP 地址上的端口。 Because containers within the group share a port namespace, port mapping isn't supported. Containers within a group can reach each other via localhost on the ports that they have exposed, even if those ports aren't exposed externally on the group's IP address.

Optionally deploy container groups into an [Azure virtual network][virtual-network] (preview) to allow containers to communicate securely with other resources in the virtual network.

## <a name="storage"></a>存储空间

可以指定要在容器组内装载的外部卷。 可以将这些卷映射到组中单个容器内的特定路径。

## <a name="common-scenarios"></a>常见方案

如果希望将单个功能性任务分成少数容器映像，则多容器组十分有用。 然后，这些映像可以由不同的团队交付并具有单独的资源需求。

使用情况示例包括：

* 为 Web 应用程序提供服务的容器和从源控件拉取最新内容的容器。
* 应用程序容器和日志记录容器。 日志记录容器收集主要应用程序输出的日志和指标并将其写入长期存储。
* 应用程序容器和监视容器。 监视容器定期向应用程序发送请求，确保该应用程序正常运行和响应，并在应用程序出现异常时发出警报。
* A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data. 

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
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
