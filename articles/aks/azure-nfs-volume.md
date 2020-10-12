---
title: 创建 NFS Ubuntu Linux 服务器卷
titleSuffix: Azure Kubernetes Service
description: 了解如何手动创建可供 Azure Kubernetes 服务 (AKS) 中的 Pod 使用的 NFS Ubuntu Linux 服务器卷
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86251988"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中手动创建和使用 NFS（网络文件系统）Linux 服务器卷
基于容器的服务和应用程序往往需要在容器之间共享数据。 通常，会有各种 Pod 需要访问外部持久性卷上的相同信息。    
Azure 文件是一个选项，而在 Azure VM 上创建的 NFS 服务器是持久性共享存储的另一种形式。 

本文将介绍如何在 Ubuntu 虚拟机上创建 NFS 服务器。 另外，将介绍 AKS 容器如何访问此共享文件系统。

## <a name="before-you-begin"></a>准备阶段
本文假设你已有一个 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

AKS 群集需要驻留在 NFS 服务器所在的相同或对等互连的虚拟网络中。 该群集必须在现有的 VNET 中创建，该 VNET 可以是 VM 所在的同一 VNET。

以下文档介绍了使用现有 VNET 进行配置的步骤：[在现有 VNET 中创建 AKS 群集][aks-virtual-network]和[通过 VNET 对等互连连接虚拟网络][peer-virtual-networks]

本文还假设你已创建一个 Ubuntu Linux 虚拟机（例如 18.04 LTS）。 可以使用任意设置和大小，并可以通过 Azure 部署该虚拟机。 有关 Linux 快速入门，请参阅 [Linux VM 管理][linux-create]。

如果你是首次部署 AKS 群集，在部署 Ubuntu 计算机时，Azure 会自动填充虚拟网络字段，使该计算机驻留在同一 VNET 中。 但是，如果你想要改用对等互连网络，请参阅上述文档。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>将 NFS 服务器部署到虚拟机
下面是用于在 Ubuntu 虚拟机中设置 NFS 服务器的脚本：
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
服务器将会重启（由于使用了该脚本），你可以将 NFS 服务器装载到 AKS。

>[!IMPORTANT]  
>请务必将 **AKS_SUBNET** 替换为群集中的适当子网；如果使用“*”，则会在 NFS 服务器中打开所有端口和连接。

创建 VM 后，将上述脚本复制到某个文件中。 然后可以使用以下命令，将该脚本从本地计算机或其所在的任意位置移到 VM 中： 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
将脚本移到 VM 后，可以通过 SSH 连接到 VM，并通过以下命令执行该脚本：
```console
sudo ./nfs-server-setup.sh
```
如果脚本由于权限被拒绝错误而执行失败，请通过以下命令设置执行权限：
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>将 AKS 群集连接到 NFS 服务器
可以通过预配一个持久性卷，以及一个指定如何访问该卷的持久性卷声明，将 NFS 服务器连接到群集。

必须连接相同或对等互连的虚拟网络中的两个服务。 下面提供了有关在同一 VNET 中设置群集的说明：[在现有 VNET 中创建 AKS 群集][aks-virtual-network]

将这些服务放入同一虚拟网络（或对等互连的虚拟网络）后，需要在 AKS 群集中预配持久性卷和持久性卷声明。 然后，容器可将 NFS 驱动器装载到其本地目录。

下面是持久性卷的示例 Kubernetes 定义（此定义假设群集和 VM 位于同一 VNET 中）：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
请将 **NFS_INTERNAL_IP**、**NFS_NAME** 和 **NFS_EXPORT_FILE_PATH** 替换为 NFS 服务器信息。

还需要一个持久性卷声明文件。 下面是该文件的内容示例：

>[!IMPORTANT]  
>**"storageClassName"** 需保留为空字符串，否则声明不起作用。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>故障排除
如果无法从群集连接到服务器，问题的原因可能是导出的目录或其父级没有足够的权限，因此无法访问服务器。

请检查导出的目录及其父目录是否具有 777 权限。

可运行以下命令来检查权限，目录应有 *'drwxrwxrwx'* 权限：
```console
ls -l
```

## <a name="more-information"></a>详细信息
如需完整的演练，或者在调试 NFS 服务器设置时需要帮助，请查看以下深度教程：
  - [NFS 教程][nfs-tutorial]

## <a name="next-steps"></a>后续步骤

如需相关的最佳做法，请参阅 [AKS 中的存储和备份最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
