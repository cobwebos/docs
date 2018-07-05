---
title: 创建 Azure Kubernetes 服务 (AKS) 群集
description: 使用 CLI 或 Azure 门户创建 AKS 群集。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029226"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>创建 Azure Kubernetes 服务 (AKS) 群集

可以使用 Azure CLI 或 Azure 门户创建 Azure Kubernetes 服务 (AKS) 群集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks create][az-aks-create] 命令创建 AKS 群集。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

`az aks create` 命令可以使用以下选项。 有关其中每个参数的详细信息，请参阅 AKS 的 [Azure CLI 参考][az-aks-create]。

| 参数 | 说明 | 必选 |
|---|---|:---:|
| `--name` `-n` | 托管群集的资源名称。 | 是 |
| `--resource-group` `-g` | Azure Kubernetes 服务资源组的名称。 | 是 |
| `--admin-username` `-u` | Linux 虚拟机的用户名。  默认值：azureuser。 | 否 |
| `--aad-client-app-id` | （预览版）“Native”类型的 Azure Active Directory 客户端应用程序的 ID。 | 否 |
| `--aad-server-app-id` | （预览版）“Web app/API”类型的 Azure Active Directory 服务器应用程序的 ID。 | 否 |
| `--aad-server-app-secret` | （预览版）Azure Active Directory 服务器应用程序的机密。 | 否 |
| `--aad-tenant-id` | （预览版）Azure Active Directory 租户的 ID。 | 否 |
| `--admin-username` `-u` | 在节点 VM 上创建用于 SSH 访问的用户帐户。  默认值：azureuser。 | 否 |
| ` --client-secret` | 与服务主体关联的机密。 | 否 |
| `--dns-name-prefix` `-p` | 群集公共 IP 地址的 DNS 前缀。 | 否 |
| `--dns-service-ip` | 分配给 Kubernetes DNS 服务的 IP 地址。 | 否 |
| `--docker-bridge-address` | 分配给 Docker 桥的 IP 地址和网络掩码。 | 否 |
| `--enable-addons` `-a` | 在逗号分隔列表中启用 Kubernetes 加载项。 | 否 |
| `--enable-rbac` `-r` | 启用 Kubernetes 基于角色的访问控制。 | 否 |
| `--generate-ssh-keys` | 生成 SSH 公钥和私钥文件（如果缺少）。 | 否 |
| `--kubernetes-version` `-k` | 要用于创建群集的 Kubernetes 版本，如“1.7.9”或“1.9.6”。 | 否 |
| `--locaton` `-l` | 自动创建的资源组的位置 | 否 |
| `--max-pods` `-m` | 可部署到节点的最大 Pod 数。 | 否 |
| `--network-plugin` | 要使用的 Kubernetes 网络插件。 | 否 |
| `--no-ssh-key` `-x` | 不要使用或创建本地 SSH 密钥。 | 否 |
| `--no-wait` | 不等待长时间运行的操作完成。 | 否 |
| `--node-count` `-c` | 节点池的默认节点数。  默认值：3。 | 否 |
| `--node-osdisk-size` | 节点池虚拟机的 osDisk 大小（以 GB 为单位）。 | 否 |
| `--node-vm-size` `-s` | 虚拟机的大小。  默认值：Standard_D1_v2。 | 否 |
| `--pod-cidr` | CIDR 表示法 IP 范围，使用 kubenet 时从其中分配 Pod IP。 | 否 |
| `--service-cidr` | CIDR 表示法 IP 范围，从其中分配服务群集 IP。 | 否 |
| `--service-principal` | 用于群集身份验证的服务主体。 | 否 |
| `--ssh-key-value` | SSH 密钥文件值或密钥文件路径。  默认值：~/.ssh/id_rsa.pub。 | 否 |
| `--tags` | 采用“键[=值]”格式的空格分隔标记。 使用 '' 清除现有标记。 | 否 |
| `--vnet-subnet-id` | 要在其中部署群集的现有 VNet 中子网的 ID。 | 否 |
| `--workspace-resource-id` | 要用于存储监视数据的现有 Log Analytics 工作空间的资源 ID。 | 否 |

## <a name="azure-portal"></a>Azure 门户

有关使用 Azure 门户部署 AKS 群集的说明，请参阅 Azure Kubernetes 服务 (AKS) [Azure 门户快速入门][aks-portal-quickstart]。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
