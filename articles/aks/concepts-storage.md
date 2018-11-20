---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的存储
description: 了解 Azure Kubernetes 服务 (AKS) 中的存储，其中包括卷、永久性卷、存储类和声明
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: c3a737bdd9978e6cb02e3e8b7a34407eb1dd8fb6
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380515"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的应用程序存储选项

在 Azure Kubernetes 服务 (AKS) 中运行的应用程序可能需要存储和检索数据。 对于某些应用程序工作负荷，此数据存储可使用节点上在 Pod 删除后即不再需要的本地快速存储。 其他应用程序工作负荷可能需要使用在 Azure 平台内更加普通的数据卷中持续保存的存储。 多个 Pod 可能需要共享相同的数据卷，或者，如果在其他节点上重新计划 Pod，则需要重新附加数据卷。 最后，你可能需要将敏感数据或应用程序配置信息注入 Pod。

![Azure Kubernetes 服务 (AKS) 群集中的应用程序存储选项](media/concepts-storage/aks-storage-options.png)

本文介绍为 AKS 中的应用程序提供存储的核心概念：

- [卷](#volumes)
- [永久性卷](#persistent-volumes)
- [存储类](#storage-classes)
- [永久性卷声明](#persistent-volume-claims)

## <a name="volumes"></a>卷

应用程序通常需要能够存储和检索数据。 由于 Kubernetes 通常将单个 Pod 视为可释放的临时资源，应用程序可根据需要通过不同方法来使用和保存数据。 *卷*表示一种跨 Pod 和应用程序生命周期存储、检索及保存数据的方法。

用于存储和检索数据的传统卷作为 Azure 存储支持的 Kubernetes 资源创建。 你可以手动创建这些数据卷并直接分配给 Pod，也可以让 Kubernetes 自动创建它们。 这些数据卷可以使用 Azure 磁盘或 Azure 文件：

- *Azure 磁盘*可用于创建 Kubernetes *DataDisk* 资源。 Azure 磁盘可以使用由高性能 SSD 支持的 Azure 高级存储，也可以使用由普通 HDD 支持 Azure 标准存储。 对于大部分生产和开发工作负荷，请使用高级存储。 Azure 磁盘以 *ReadWriteOnce* 的形式装载，因此仅可用于单个节点。 对于可以同时由多个节点访问的存储卷，请使用 Azure 文件。
- *Azure 文件*可用于将 Azure 存储帐户支持的 SMB 3.0 共享装载到 Pod。 借助 Azure 文件,可跨多个节点和 Pod 共享数据。 目前，Azure 文件只能使用普通 HDD 支持的 Azure 标准存储。

在 Kubernetes 中，卷不仅仅能够表示可以存储和检索信息的传统磁盘。 Kubernetes 卷还可以用于将数据注入 Pod 以供容器使用。 Kubernetes 中常见的其他卷类型包括：

- *emptyDir*：此卷通常用作 Pod 的临时空间。 Pod 中的所有容器都可以访问卷上的数据。 写入此卷类型的数据仅在 Pod 的生命周期内持续保存，当 Pod 被删除时，卷也会删除。 此卷通常使用基础本地节点磁盘存储，但也可以仅存在于节点的内存中。
- *secret*：此卷用于将敏感数据注入 Pod，例如密码。 首先使用 Kubernetes API 创建机密。 在定义 pod 或部署时，可以请求特定机密。 机密仅提供给所计划的 pod 需要该机密的节点，且机密存储在 *tmpfs* 中，不写入磁盘。 当节点上最后一个需要该机密的 pod 被删除后，将从该节点的 tmpfs 中删除该机密。 机密存储在给定的命名空间中，只有同一命名空间中的 pod 能访问该机密。
- *configMap*：此卷类型用于将键-值对属性注入 Pod，例如应用程序配置信息。 无需在容器映像中定义应用程序配置信息，而是可以将其定义为 Kubernetes 资源，以便在部署新 Pod 实例时可轻松为其更新并应用。 与使用 secret 一样，必须先使用 Kubernetes API 创建 ConfigMap。 随后可在定义 Pod 或部署时请求此 ConfigMap。 ConfigMap 存储在给定命名空间内，且只能由同一命名空间中的 Pod 访问。

## <a name="persistent-volumes"></a>永久性卷

卷作为 Pod 生命周期的一部分定义和创建，且仅在删除 Pod 之前存在。 如果在维护事件期间（尤其是在 StatefulSets 中）于另一台主机上重新计划 Pod，则 Pod 通常会预期其存储会被保留。 *永久性卷* (PV) 是由 Kubernetes API 创建和管理的存储资源，可以在单个 Pod 的生命周期之外存在。

Azure 磁盘或文件用于提供 PersistentVolume。 如上一部分中对卷所做的说明，选择磁盘还是文件通常取决于并发访问数据或性能层的需求。

![Azure Kubernetes 服务 (AKS) 群集中的永久性卷](media/concepts-storage/persistent-volumes.png)

PersistentVolume 可以由群集管理员*静态*创建，或者由 Kubernetes API 服务器*动态*创建。 如果已计划 Pod 并请求当前不可用的存储，则 Kubernetes 可以创建基础 Azure 磁盘或文件存储并将其附加到 Pod。 动态预配使用 *StorageClass* 来标识需要创建的 Azure 存储类型。

## <a name="storage-classes"></a>存储类

若要定义不同的存储层（例如高级和标准），可创建 *StorageClass*。 StorageClass 还定义 *reclaimPolicy*。 删除 Pod 后且可能不再需要永久性卷时，此 reclaimPolicy 可控制基础 Azure 存储资源在此情况下的行为。 可删除基础存储资源，也可保留基础存储资源以便与未来的 Pod 配合使用。

在 AKS 中会创建两个初始 StorageClass：

- *default*：使用 Azure 标准存储创建托管磁盘。 回收策略会指出，在使用基础 Azure 磁盘的 Pod 被删除时，即应删除该磁盘。
- *managed-premium*：使用 Azure 高级存储创建托管磁盘。 回收策略同样会指出，在使用基础 Azure 磁盘的 Pod 被删除时，即应删除该磁盘。

如果没有为永久性卷指定 StorageClass，则会使用默认 StorageClass。 请求永久性卷时应小心，以便它们使用你需要的适当存储。 可使用 `kubectl` 创建 StorageClass 来满足其他需求。 以下示例使用高级托管磁盘并指定在删除 Pod 时应该*保留*基础 Azure 磁盘：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>永久性卷声明

PersistentVolumeClaim 会请求特定 StorageClass、访问模式和大小的磁盘或文件存储。 如果根据定义的 StorageClass 没有现有资源可用于实现声明，Kubernetes API 服务器可在 Azure 中动态预配基础存储资源。 卷连接到 Pod 后，Pod 定义即会包含装载。

![Azure Kubernetes 服务 (AKS) 群集中的永久性卷声明](media/concepts-storage/persistent-volume-claims.png)

可用存储资源分配给请求它的 Pod 后，PersistentVolume 就会*绑定*到 PersistentVolumeClaim。 永久性卷与声明之间存在 1:1 的映射。

以下示例 YAML 清单显示使用 *managed-premium* StorageClass 并请求 *5Gi* 存储的永久性卷声明：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

创建 Pod 定义时，将指定永久性卷声明来请求所需的存储。 随后还会为应用程序指定用于读取和写入数据的 *volumeMount*。 以下示例 YAML 清单说明如何使用先前的永久性卷声明来将卷装载到 */mnt/azure*：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>后续步骤

若要了解如何创建使用 Azure 磁盘或 Azure 文件的动态和静态卷，请参阅以下操作指南：

- [使用 Azure 磁盘创建静态卷][aks-static-disks]
- [使用 Azure 文件创建静态卷][aks-static-files]
- [使用 Azure 磁盘创建动态卷][aks-dynamic-disks]
- [使用 Azure 文件创建动态卷][aks-dynamic-files]

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 规模][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
