---
title: 了解 Azure Stack Edge 设备上的 Kubernetes 工作负荷管理 |Microsoft Docs
description: 介绍如何在 Azure Stack 边缘设备上管理 Kubernetes 工作负荷。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083140"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Azure Stack 边缘设备上的 Kubernetes 工作负荷管理

在 Azure Stack Edge 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，可以在 pod 中的 Kubernetes 群集上部署容器化应用程序。 可以通过不同的方式在 Kubernetes 群集中部署工作负荷。 

本文介绍可用于在 Azure Stack Edge 设备上部署工作负荷的各种方法。

## <a name="workload-types"></a>工作负荷类型

可在 Azure Stack Edge 设备上部署的两种常见的工作负荷类型为无状态应用程序或有状态应用程序。

- **无状态应用程序** 不会保留其状态，也不会将任何数据保存到永久性存储。 所有用户和会话数据都在客户端上。 无状态应用程序的一些示例包括 web 前端（如 Nginx）和其他 web 应用程序。

    你可以创建 Kubernetes 部署，以便在群集上部署无状态应用程序。 

- 有**状态应用程序**要求保存其状态。 有状态应用程序使用持久存储（例如永久卷）保存数据，供服务器或其他用户使用。 有状态应用程序的示例包括 MongoDB 之类的数据库。

    你可以创建 Kubernetes 部署来部署有状态应用程序。 

## <a name="namespaces-types"></a>命名空间类型

Kubernetes 资源（如 pod 和部署）按逻辑分组到一个命名空间中。 这些分组提供了一种逻辑划分 Kubernetes 群集的方法，并限制了创建、查看或管理资源的访问权限。 用户只能与分配的命名空间内的资源进行交互。

命名空间适用于具有多个用户分布在多个团队或项目中的环境。 对于包含少量用户的群集，不需要创建或考虑所有命名空间。 当你需要提供的功能时，开始使用命名空间。

有关详细信息，请参阅 [Kubernetes 命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。


Azure Stack 边缘设备包含以下命名空间：

- **系统命名空间** -此命名空间是核心资源所在的位置，例如 DNS 和代理等网络功能，或 Kubernetes 仪表板。 通常不会将应用程序部署到此命名空间中。 使用此命名空间调试任何 Kubernetes 群集问题。 

    设备上有多个系统命名空间，并且保留了与这些系统命名空间对应的名称。 下面是保留系统命名空间的列表： 
    - kube-系统
    - metallb-系统
    - dbe-命名空间
    - default
    - kubernetes-dashboard
    - default
    - kube-租赁
    - kube-公共
    - iotedge
    - azure-弧线

    请确保不要将任何保留名称用于创建的用户命名空间。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **用户命名空间** -这些是可以通过 **kubectl** 创建的命名空间，以本地方式部署应用程序。
 
- **IoT Edge 命名空间** -通过 IoT Edge 连接到此 `iotedge` 命名空间以部署应用程序。

- **Azure arc 命名空间** - `azure-arc` 通过 azure arc 连接到此命名空间以部署应用程序。

 
## <a name="deployment-types"></a>部署类型

有三种主要方法可用于部署工作负荷。 其中每种部署方法允许连接到设备上的不同命名空间，然后部署无状态或有状态的应用程序。

![Kubernetes 工作负荷部署](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **本地部署**：这是通过命令行访问工具（例如） `kubectl` 来部署 K8 `yamls` 。 您可以使用该文件连接到您 Azure Stack 上的 K8 群集 `kubeconfig` 。 有关详细信息，请参阅 [通过 Kubectl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

- **IoT Edge 部署**：这是通过 IoT Edge 连接到 Azure IoT 中心。 通过命名空间连接到 Azure Stack 边缘设备上的 K8 群集 `iotedge` 。 部署在此命名空间中的 IoT Edge 代理负责连接到 Azure。 `IoT Edge deployment.json`使用 Azure DEVOPS CI/CD 应用配置。 命名空间和 IoT Edge 管理通过 cloud operator 完成。

- **Azure/Arc 部署**： azure Arc 是一种混合管理工具，可用于在 K8 群集上部署应用程序。 可以通过在 Azure Stack 边缘设备上连接 K8 群集 `azure-arc namespace` 。  代理部署在此命名空间中，该命名空间负责连接到 Azure。 使用基于 GitOps 的配置管理应用部署配置。 Azure Arc 还允许使用容器 Azure Monitor 来查看和监视群集。 有关详细信息，请参阅 [什么是启用了 Azure Arc 的 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。

## <a name="choose-the-deployment-type"></a>选择部署类型

在部署应用程序时，请考虑以下信息：

- **单个或多个类型**：可以选择单个部署选项或不同部署选项的混合。
- **云与本地**：根据应用程序，可以通过 kubectl 或云部署通过 IoT Edge 和 Azure Arc 选择本地部署。 
    - 本地部署更适合开发方案。 选择本地部署时，将限制为在其中部署 Azure Stack Edge 设备的网络。
    - 如果你有可以部署的云代理，则应该部署云操作员，并使用云管理。
- **IoT Vs Azure Arc**：部署选择还取决于产品方案的目的。 如果要部署的应用程序或容器与 IoT 或 IoT 生态系统更深层的集成，则应该选择部署应用程序 IoT Edge 方式。 如果你有现有的 Kubernetes 部署，Azure Arc 将是首选选项。


## <a name="next-steps"></a>后续步骤

若要通过 kubectl 本地部署应用，请参阅：

- [通过 kubectl 在 Azure Stack 边缘部署无状态应用程序](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

若要通过 IoT Edge 部署应用，请参阅：

- [通过 IoT Edge 在 Azure Stack 边缘部署示例模块](azure-stack-edge-gpu-deploy-sample-module.md)。

若要通过 Azure Arc 部署应用，请参阅：

- [使用 Azure Arc 部署应用程序](azure-stack-edge-gpu-deploy-sample-module.md)。
