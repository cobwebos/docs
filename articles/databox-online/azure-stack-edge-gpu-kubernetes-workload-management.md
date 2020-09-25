---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 工作负荷管理 |Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro 设备上管理 Kubernetes 工作负荷。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: d9e0da9e24a0bd32047d029879c4f0e110dc0c16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320789"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 设备上的 Kubernetes 工作负荷管理

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，可以在 pod 中的 Kubernetes 群集上部署容器化应用程序。 可以通过不同的方式在 Kubernetes 群集中部署工作负荷。 

本文介绍可用于在 Azure Stack Edge Pro 设备上部署工作负荷的各种方法。

## <a name="workload-types"></a>工作负荷类型

可在 Azure Stack Edge Pro 设备上部署的两种常见的工作负荷类型为无状态应用程序或有状态应用程序。

- **无状态应用程序** 不会保留其状态，也不会将任何数据保存到永久性存储。 所有用户和会话数据都在客户端上。 无状态应用程序的一些示例包括 web 前端（如 Nginx）和其他 web 应用程序。

    你可以创建 Kubernetes 部署，以便在群集上部署无状态应用程序。 

- 有**状态应用程序**要求保存其状态。 有状态应用程序使用持久存储（例如永久卷）保存数据，供服务器或其他用户使用。 有状态应用程序的示例包括 [AZURE SQL Edge](../azure-sql-edge/overview.md) 和 MongoDB 之类的数据库。

    你可以创建 Kubernetes 部署来部署有状态应用程序。 

## <a name="deployment-flow"></a>部署流

若要在 Azure Stack Edge Pro 设备上部署应用程序，请执行以下步骤： 
 
1. **配置访问**：首先，你将使用 PowerShell 运行空间来创建用户、创建命名空间，并向用户授予对该命名空间的访问权限。
2. **配置存储**：接下来，你将使用 Azure 门户中的 Azure Stack Edge 资源为要部署的有状态应用程序使用静态或动态预配创建持久卷。
3. **配置网络**：最后，你将使用服务在 Kubernetes 群集外部公开应用程序。
 
## <a name="deployment-types"></a>部署类型

有三种主要方法可用于部署工作负荷。 其中每种部署方法允许连接到设备上的不同命名空间，然后部署无状态或有状态的应用程序。

![Kubernetes 工作负荷部署](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **本地部署**：此部署通过命令行访问工具（例如） `kubectl` 来部署 Kubernetes `yamls` 。 通过文件访问 Azure Stack Edge Pro 上的 Kubernetes 群集 `kubeconfig` 。 有关详细信息，请参阅 [通过 Kubectl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

- **IoT Edge 部署**：这是通过 IoT Edge 连接到 Azure IoT 中心。 通过命名空间连接到 Azure Stack Edge Pro 设备上的 Kubernetes 群集 `iotedge` 。 部署在此命名空间中的 IoT Edge 代理负责连接到 Azure。 `IoT Edge deployment.json`使用 Azure DEVOPS CI/CD 应用配置。 命名空间和 IoT Edge 管理通过 cloud operator 完成。

- **启用 Azure Arc Kubernetes 部署**：启用了 azure Arc 的 Kubernetes 是一种混合管理工具，可用于在 Kubernetes 群集上部署应用程序。 可以通过在 Azure Stack Edge Pro 设备上连接到 Kubernetes 群集 `azure-arc namespace` 。 此命名空间中部署的代理负责连接到 Azure。 使用基于 GitOps 的配置管理应用部署配置。 
    
    启用 Azure Arc 后，Kubernetes 还将允许使用容器 Azure Monitor 来查看和监视群集。 有关详细信息，请参阅 [什么是启用了 Azure Arc 的 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。

## <a name="choose-the-deployment-type"></a>选择部署类型

在部署应用程序时，请考虑以下信息：

- **单个或多个类型**：可以选择单个部署选项或不同部署选项的混合。
- **云与本地**：根据应用程序，可以通过 kubectl 或云部署通过 IoT Edge 和 Azure Arc 选择本地部署。 
    - 选择本地部署时，将限制为在其中部署 Azure Stack Edge Pro 设备的网络。
    - 如果你有可以部署的云代理，则应该部署云操作员，并使用云管理。
- **IoT Vs Azure Arc**：部署选择还取决于产品方案的目的。 如果要部署的应用程序或容器与 IoT 或 IoT 生态系统更深层的集成，请选择 "IoT Edge" 以部署应用程序。 如果你有现有的 Kubernetes 部署，Azure Arc 将是首选选项。


## <a name="next-steps"></a>后续步骤

若要通过 kubectl 本地部署应用，请参阅：

- [通过 kubectl 在 Azure Stack Edge Pro 上部署无状态应用程序](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

若要通过 IoT Edge 部署应用，请参阅：

- [通过 IoT Edge 在 Azure Stack Edge Pro 上部署示例模块](azure-stack-edge-gpu-deploy-sample-module.md)。

若要通过 Azure Arc 部署应用，请参阅：

- [使用 Azure Arc 部署应用程序](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。
