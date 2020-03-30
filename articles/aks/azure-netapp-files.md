---
title: 将 Azure NetApp 文件与 Azure 库伯奈斯服务集成
description: 了解如何将 Azure NetApp 文件与 Azure 库伯奈斯服务集成
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273750"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>将 Azure NetApp 文件与 Azure 库伯奈斯服务集成

[Azure NetApp 文件][anf]是在 Azure 上运行的企业级高性能、按流量计费的文件存储服务。 本文介绍如何将 Azure NetApp 文件与 Azure 库伯奈斯服务 （AKS） 集成。

## <a name="before-you-begin"></a>开始之前
本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> AKS 群集还必须[位于支持 Azure NetApp 文件的区域][anf-regions]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

使用 Azure NetApp 文件时，以下限制适用：

* Azure NetApp 文件仅在[选定的 Azure 区域][anf-regions]中可用。
* 在使用 Azure NetApp 文件之前，必须授予对 Azure NetApp 文件服务的访问权限。 要申请访问，可以使用[Azure NetApp 文件等待列表提交窗体][anf-waitlist]。 在收到 Azure NetApp 文件团队的官方确认电子邮件之前，无法访问 Azure NetApp 文件服务。
* Azure NetApp 文件服务必须在与 AKS 群集相同的虚拟网络中创建。
* 在初始部署 AKS 群集后，仅支持 Azure NetApp 文件的静态预配。
* 要将动态预配与 Azure NetApp 文件一起使用，请安装和配置[NetApp Trident](https://netapp-trident.readthedocs.io/)版本 19.07 或更高版本。

## <a name="configure-azure-netapp-files"></a>配置 Azure NetApp 文件

> [!IMPORTANT]
> 必须先完成订阅的[Azure NetApp 文件等待列表提交表单][anf-waitlist]，然后才能注册*Microsoft.NetApp*资源提供程序。 在收到 Azure NetApp 文件团队的官方确认电子邮件之前，无法注册资源提供。

注册*Microsoft.NetApp*资源提供商：

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 此操作需要一段时间才能完成。

创建用于 AKS 的 Azure NetApp 帐户时，需要在**节点**资源组中创建该帐户。 首先，使用 [az aks show][az-aks-show] 命令获取资源组名称并添加 `--query nodeResourceGroup` 查询参数。 下面的示例获取名为 myAKSCluster 的资源组名为*myAKSCluster*的 AKS 群集的节点资源组，该组名为*myResourceGroup*：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

使用[az netapp 文件帐户][az-netappfiles-account-create]创建在**节点**资源组中创建 Azure NetApp 文件帐户，并使用 与 AKS 群集相同的区域。 以下示例在*MC_myResourceGroup_myAKSCluster_eastus*资源组和东部区域中创建名为*myaccount1* *的*帐户：

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

使用[az netapp文件池创建][az-netappfiles-pool-create]创建新的容量池。 下面的示例创建一个名为*mypool1*的新容量池，其大小和*高级*服务级别为 4 TB：

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

创建子网以使用[az 网络 vnet 子网创建][az-network-vnet-subnet-create][将委托给 Azure NetApp 文件][anf-delegate-subnet]。 *此子网必须与 AKS 群集位于同一虚拟网络中。*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

使用[az netappfile 卷创建创建卷][az-netappfiles-volume-create]。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>创建持久卷

使用[az netapp文件卷显示][az-netappfiles-volume-show]列出卷的详细信息

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

创建定义`pv-nfs.yaml`持久卷。 替换为`path`*创建令牌*，然后`server`替换为上一个命令中的*ipAddress。* 例如：

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

更新*服务器*和*路径*，以访问在上一步骤中创建的 NFS（网络文件系统）卷的值。 使用[kubectl 应用][kubectl-apply]命令创建持久卷：

```console
kubectl apply -f pv-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]命令验证持久卷*的状态**可用*：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>创建持久卷索赔

创建定义`pvc-nfs.yaml`持久卷。 例如：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

使用[kubectl 应用][kubectl-apply]命令创建持久卷声明：

```console
kubectl apply -f pvc-nfs.yaml
```

*使用*[kubectl 描述][kubectl-describe]命令验证持久卷声明*的状态*：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>随吊舱安装

创建`nginx-nfs.yaml`使用持久卷声明的窗格定义。 例如：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

使用[kubectl 应用][kubectl-apply]命令创建 pod：

```console
kubectl apply -f nginx-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]命令验证窗格*正在运行*：

```console
kubectl describe pod nginx-nfs
```

使用[kubectl exec][kubectl-exec]连接到 pod，然后`df -h`检查卷是否已安装，从而验证卷已安装在窗格中。

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>后续步骤

有关 Azure NetApp 文件的详细信息，请参阅[什么是 Azure NetApp 文件][anf]。 有关将 NFS 与 AKS 一起使用的详细信息，请参阅[手动创建和使用具有 Azure 库伯奈斯服务 （AKS） 的 NFS（网络文件系统）Linux 服务器卷][aks-nfs]。


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
