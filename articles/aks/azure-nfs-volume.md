---
title: 创建由 Azure Kubernetes Service （AKS）的 pod 使用的 NFS （网络文件系统） Ubuntu 服务器
description: 了解如何在 Azure Kubernetes Service （AKS）中手动创建 NFS Ubuntu Linux 服务器卷以用于 pod
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 3ef584c48ab44fd3616b5c7897d589bddbe45dc0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549251"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service （AKS）手动创建和使用 NFS （网络文件系统） Linux 服务器卷
在容器之间共享数据通常是基于容器的服务和应用程序的必需组件。 通常有各种 pod 需要访问外部永久性卷上的相同信息。    
虽然可以选择 Azure 文件，但在 Azure VM 上创建 NFS 服务器是另一种持久共享存储。 

本文介绍如何在 Ubuntu 虚拟机上创建 NFS 服务器。 同时，还可让 AKS 容器访问此共享文件系统。

## <a name="before-you-begin"></a>开始之前
本文假定你已有一个 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

AKS 群集将需要与 NFS 服务器位于同一虚拟网络或对等互连虚拟网络中。 群集必须在现有的 VNET 中创建，该 VNET 可以是与 VM 相同的 VNET。

文档：在[现有 vnet 中创建 AKS 群集][aks-virtual-network]和[使用 VNET 对等互连连接虚拟网络][peer-virtual-networks]中介绍了使用现有 vnet 进行配置的步骤。

它还假定已创建 Ubuntu Linux 虚拟机（例如，18.04 LTS）。 设置和大小可以根据自己的喜好，也可以通过 Azure 部署。 有关 Linux 快速入门，请参阅[LINUX VM 管理][linux-create]。

如果首先部署 AKS 群集，则在部署 Ubuntu 计算机时，Azure 会自动填充虚拟网络字段，使其在同一 VNET 中运行。 但是，如果你想要改为使用对等互连网络，请参阅上面的文档。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>在虚拟机上部署 NFS 服务器
下面是在 Ubuntu 虚拟机中设置 NFS 服务器的脚本：
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
服务器将重新启动（由于脚本），可以将 NFS 服务器装载到 AKS

>[!IMPORTANT]  
>请确保将**AKS_SUBNET**替换为群集中的正确一个，否则 "*" 会将 NFS 服务器打开到所有端口和连接。

创建 VM 后，将上面的脚本复制到一个文件中。 然后，你可以使用以下方法将其从本地计算机或脚本中的任何位置移动到 VM： 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
一旦你的脚本位于 VM 中，你就可以通过 ssh 连接到 VM 并通过命令执行它：
```console
sudo ./nfs-server-setup.sh
```
如果因权限拒绝错误而导致其执行失败，请通过命令设置执行权限：
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>将 AKS 群集连接到 NFS 服务器
可以通过预配永久性卷和指定如何访问卷的永久卷声明，将 NFS 服务器连接到群集。  
需要将两个服务连接到相同或对等互连的虚拟网络中。 在同一 VNET 中设置群集的说明如下所示：[在现有 VNET 中创建 AKS 群集][aks-virtual-network]

一旦它们位于同一个虚拟网络（或对等互连）中，你需要在 AKS 群集中预配一个永久性卷和一个永久性卷声明。 然后，容器可以将 NFS 驱动器装载到本地目录。

下面是永久性卷的示例 Kubernetes 定义（此定义假定群集和 VM 位于同一 VNET 中）：

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
将**NFS_INTERNAL_IP**、 **NFS_NAME**和**NFS_EXPORT_FILE_PATH**替换为 NFS 服务器信息。

还需要一个永久性卷声明文件。 下面是包含内容的示例：

>[!IMPORTANT]  
>**"storageClassName"** 需要保留空字符串或声明不起作用。

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
如果无法从群集连接到服务器，则问题可能是导出的目录或其父目录没有足够的权限访问服务器。

检查导出目录及其父目录是否具有777权限。

你可以通过运行以下命令来检查权限，并且目录应具有 *"drwxrwxrwx"* 权限：
```console
ls -l
```

## <a name="more-information"></a>详细信息
若要获取完整的演练或帮助调试 NFS 服务器安装程序，请参阅下面的详细教程：
  - [NFS 教程][nfs-tutorial]

## <a name="next-steps"></a>后续步骤

有关相关的最佳实践，请参阅[AKS 中存储和备份的最佳实践][operator-best-practices-storage]。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
