---
title: 在 Azure Kubernetes 服务 (AKS) 中为多个 Pod 动态创建文件卷
description: 了解如何使用 Azure 文件动态创建永久性卷，以便与 Azure Kubernetes 服务 (AKS) 中的多个并发 Pod 一起使用
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 999e106240a8a1d95c35d098062d474a0b57228d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231759"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中动态创建永久性卷并将其用于 Azure 文件

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 If multiple pods need concurrent access to the same storage volume, you can use Azure Files to connect using the [Server Message Block (SMB) protocol][smb-overview]. 本文介绍如何动态创建 Azure 文件共享以供 Azure Kubernetes 服务 (AKS) 群集中的多个 Pod 使用。

For more information on Kubernetes volumes, see [Storage options for applications in AKS][concepts-storage].

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. 运行  `az --version` 即可查找版本。 If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>创建存储类

存储类用于定义如何创建 Azure 文件共享。 A storage account is automatically created in the [node resource group][node-resource-group] for use with the storage class to hold the Azure file shares. Choose of the following [Azure storage redundancy][storage-skus] for *skuName*:

* *Standard_LRS* - 标准本地冗余存储 (LRS)
* *Standard_GRS* - 标准异地冗余存储 (GRS)
* *Standard_RAGRS* - 标准读取访问异地冗余存储 (RA-GRS)
* *Premium_LRS* - premium locally redundant storage (LRS)

> [!NOTE]
> Azure Files support premium storage in AKS clusters that run Kubernetes 1.13 or higher.

For more information on Kubernetes storage classes for Azure Files, see [Kubernetes Storage Classes][kubernetes-storage-classes].

创建名为 `azure-file-sc.yaml` 的文件，并将其复制到以下示例清单中。 For more information on *mountOptions*, see the [Mount options][mount-options] section.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Create the storage class with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明 (PVC) 使用存储类对象来动态预配 Azure 文件共享。 The following YAML can be used to create a persistent volume claim *5 GB* in size with *ReadWriteMany* access. For more information on access modes, see the [Kubernetes persistent volume][access-modes] documentation.

现在，创建名为 `azure-file-pvc.yaml` 的文件，并将其复制到以下 YAML 中。 请确保 *storageClassName* 与上一步骤中创建的存储类匹配：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> If using the *Premium_LRS* sku for your storage class, the minimum value for *storage* must be *100Gi*.

Create the persistent volume claim with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-pvc.yaml
```

完成此步骤后，文件共享即创建完毕。 同时还会创建一个包含连接信息和凭据的 Kubernetes 机密。 You can use the [kubectl get][kubectl-get] command to view the status of the PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>使用永久性卷

以下 YAML 创建的 Pod 使用永久性卷声明 *azurefile* 将 Azure 文件共享装载到 */mnt/azure* 路径。 For Windows Server containers (currently in preview in AKS), specify a *mountPath* using the Windows path convention, such as *'D:'* .

创建名为 `azure-pvc-files.yaml` 的文件，并将其复制到以下 YAML 中。 请确保 *claimName* 与上一步骤中创建的 PVC 匹配。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Create the pod with the [kubectl apply][kubectl-apply] command.

```console
kubectl apply -f azure-pvc-files.yaml
```

现有一个正在运行的 Pod，其中的 Azure 文件存储共享已装载到 /mnt/azure 目录中。 通过 `kubectl describe pod mypod` 检查 pod 时，可以看到此配置。 以下精简示例输出显示容器中装载的卷：

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>装载选项

The default value for *fileMode* and *dirMode* is *0755* for Kubernetes version 1.9.1 and above. If using a cluster with Kuberetes version 1.8.5 or greater and dynamically creating the persistent volume with a storage class, mount options can be specified on the storage class object. 以下示例设置 *0777*：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

如果使用版本为 1.8.0 - 1.8.4 的群集，则可在指定安全性上下文时，将 *runAsUser* 值设置为 *0*。 For more information on Pod security context, see [Configure a Security Context][kubernetes-security-context].

## <a name="next-steps"></a>后续步骤

For associated best practices, see [Best practices for storage and backups in AKS][operator-best-practices-storage].

详细了解使用 Azure 文件的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 文件的 Kubernetes 插件][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks