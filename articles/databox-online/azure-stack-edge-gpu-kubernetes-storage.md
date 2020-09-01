---
title: 了解 Azure Stack Edge 设备上的 Kubernetes 存储管理 |Microsoft Docs
description: 介绍如何在 Azure Stack 边缘设备上进行 Kubernetes 存储管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 57574b66ddb20e592a5979a4b827347f7c8e09af
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268085"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-gpu-device"></a>Azure Stack 边缘 GPU 设备上的 Kubernetes 存储管理

在 Azure Stack Edge 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，可以在 pod 中的 Kubernetes 群集上部署容器化应用程序。 可以通过不同的方式在 Kubernetes 群集中将存储提供给 pod。 

本文介绍一般情况下在 Kubernetes 群集上预配存储的方法，尤其是在 Azure Stack Edge 设备的上下文中。 

## <a name="storage-requirements-for-kubernetes-pods"></a>Kubernetes pod 的存储要求

Kubernetes pod 是无状态的，但其运行的应用程序通常有状态。 由于 pod 可以短时间段，并且在 Kubernetes 节点之间重新启动、故障转移或移动，因此，对于与 pod 关联的存储，必须满足以下要求。 

存储必须：

- 位于 pod 外。
- 独立于 pod 生命周期。
- 可从所有 Kubernetes 节点访问。

若要了解如何为 Kubernetes 管理存储，需要了解两个 API 资源： 

- **PersistentVolume (PV) **：这是 Kubernetes 群集中的一部分存储。 可以静态方式将 Kubernetes 存储设置为 `PersistentVolume` 。 它也可以动态设置为  `StorageClass` 。

- **PersistentVolumeClaim (PVC) **：这是用户对存储的请求。 Pvc 使用 PV 资源。 Pvc 可以请求特定的大小和访问模式。 

    由于用户需要 `PersistentVolumes` 具有不同的属性来解决不同的问题，因此群集管理员需要能够提供多种不同于 `PersistentVolumes` 大小和访问模式的方式。 为了满足这些需求，需要 `StorageClass` 资源。

存储设置可以是静态或动态的。 以下各节讨论了每种设置类型。

## <a name="staticprovisioning"></a>静态预配

Kubernetes 群集管理员可以通过静态方式预配存储。 为此，他们可以使用基于 SMB/NFS 文件系统的存储后端，或者使用在本地环境中通过网络本地附加的 iSCSI 磁盘，甚至可以在云中使用 Azure 文件或 Azure 磁盘。 默认情况下不设置此存储类型，并且群集管理员必须计划和管理此设置。 
 
以下图表描述了如何在 Kubernetes 中使用静态预配的存储： 

![通过 PersistentVolumes 进行的静态预配](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

将执行以下步骤： 

1. **设置存储**：群集管理员预配存储。 在此示例中，群集管理员创建一个或多个 SMB 共享，以便在与这些共享相对应的 Kubernetes 群集中自动创建永久卷对象。 

1. **声明存储**：提交请求存储的 PVC 部署。 此存储声明是 PersistentVolumeClaim (PVC) 。 如果 PV 的大小和访问模式与 PVC 的大小和访问模式相匹配，则 PVC 将绑定到 PV。 PVC 和 PV 映射一对一映射。

1. 将**Pvc 装载到容器**：将 pvc 绑定到 PV 后，可以将该 pvc 装载到容器中的路径上。 当容器中的应用程序逻辑从/向此路径中读取/写入数据时，数据将写入 SMB 存储。
 

## <a name="dynamicprovisioning"></a>动态预配

以下图表描述了如何在 Kubernetes 中使用静态预配的存储： 

![通过 StorageClasses 进行动态预配](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

将执行以下步骤： 

1. **定义存储类**：群集管理定义存储类，具体取决于 Kubernetes 群集的操作环境。 群集管理员还会部署配置程序，它还部署在 Kubernetes 群集上的另一个 pod 或应用程序。 配置程序提供了动态设置共享的所有详细信息。  

1. **声明存储**：提交将声明存储的应用程序。 使用此存储类引用创建 PVC 后，将调用配置程序。 

1. **动态预配存储**：配置程序动态创建与本地磁盘存储关联的共享。 创建共享后，它还会自动创建对应于此共享的 PV 对象。

1. **将 Pvc 装载到容器**：将 pvc 绑定到 PV 后，可以使用与静态设置相同的方式将 pvc 装载到路径上，也可以在共享中进行读取或写入。


## <a name="storage-provisioning-on-azure-stack-edge"></a>Azure Stack Edge 上的存储设置

在 Azure Stack 边缘设备上，静态预配 `PersistentVolumes` 是使用设备的存储功能创建的。 当你预配共享并 **使用 "使用边缘进行共享计算** " 选项时，此操作会在 Kubernetes 群集中自动创建 PV 资源。

![用于静态预配的 Azure 门户中的本地共享创建](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

若要使用云分层，可以通过启用 "使用共享并启用边缘计算" 选项创建边缘云共享。 将再次为此共享自动创建 PV。 写入边缘共享的任何应用程序数据都分层到了云。 

![用于静态预配的 Azure 门户中的云共享创建](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

你可以创建 SMB 和 NFS 共享，以便在 Azure Stack Edge 设备上以静态方式预配 PVs。 预配 PV 后，你将提交一个 PVC 来声明此存储。 下面是一个 PVC 部署示例 `yaml` ，用于声明存储并使用预配的共享。


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

有关详细信息，请参阅 [通过 kubectl 在 Azure Stack 边缘通过静态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

Azure Stack 边缘还具有一个 `StorageClass` 名为 `ase-node-local` 的内置，它使用附加到 Kubernetes 节点的数据磁盘存储。 这 `StorageClass` 支持动态设置。 您可以 `StorageClass` 在 pod 应用程序中进行引用，并为您自动创建 PV。 有关详细信息，请参阅 [Kubernetes 仪表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) 以查询 `ase-node-local StorageClass` 。

![Kubernetes 仪表板中的内置存储类](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

有关详细信息，请参阅 [通过 kuebctl 在 Azure Stack 边缘通过动态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。

## <a name="choose-storage-type"></a>选择存储类型

可能需要根据要部署的工作负荷选择存储类型。 

- 如果你希望在 `ReadWriteMany` `PersistentVolumes` 部署时，将卷以读写方式装载到的访问模式，请使用 SMB/NFS 共享的静态设置。

- 如果要部署的应用程序具有 POSIX 相容性要求，例如 MongoDB、PostgreSQL、MySQL 或 Prometheus 等应用程序，请使用内置 StorageClass。 访问模式为， `ReadWriteOnce` 或卷由单个节点作为读写方式装入。 


有关访问模式的详细信息，请参阅 [Kubernetes 卷访问模式](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。


## <a name="next-steps"></a>后续步骤

若要了解如何以静态方式预配 `PersistentVolume` ，请参阅：

- 通过[kubectl 在 Azure Stack 边缘通过静态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

若要了解如何动态预配 `StorageClass` ，请参阅：

- 通过[kuebctl 在 Azure Stack 边缘通过动态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。
