---
title: "创建 Azure 容器服务 (AKS) 群集"
description: "使用 CLI 或 Azure 门户创建 AKS 群集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e884bc16680d74801911547045deb48246afccd
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-service-aks-cluster"></a>创建 Azure 容器服务 (AKS) 群集

可以使用 Azure CLI 或 Azure 门户创建 Azure 容器服务 (AKS) 群集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks create][az-aks-create] 命令删除 AKS 群集。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

`az aks create` 命令可以使用以下选项。

| 参数 | 说明 | 必选 |
|---|---|:---:|
| `--name` `-n` | 托管群集的资源名称。 | 是 |
| `--resource-group` `-g` | Azure 容器服务资源组的名称。 | 是 |
| `--admin-username` `-u` | Linux 虚拟机的用户名。  默认值：azureuser。 | 否 |
| ` --client-secret` | 与服务主体关联的机密。 | 否 |
| `--dns-name-prefix` `-p` | 群集公共 IP 地址的 DNS 前缀。 | 否 |
| `--generate-ssh-keys` | 生成 SSH 公钥和私钥文件（如果缺少）。 | 否 |
| `--kubernetes-version` `-k` | 要用于创建群集的 Kubernetes 版本，如“1.7.9”或“1.8.2”。  默认值：1.7.7。 | 否 |
| `--no-wait` | 不等待长时间运行的操作完成。 | 否 |
| `--node-count` `-c` | 节点池的默认节点数。  默认值：3。 | 否 |
| `--node-osdisk-size` | 节点池虚拟机的 osDisk 大小（以 GB 为单位）。 | 否 |
| `--node-vm-size` `-s` | 虚拟机的大小。  默认值：Standard_D1_v2。 | 否 |
| `--service-principal` | 用于群集身份验证的服务主体。 | 否 |
| `--ssh-key-value` | SSH 密钥文件值或密钥文件路径。  默认值：~/.ssh/id_rsa.pub。 | 否 |
| `--tags` | 采用“键[=值]”格式的空格分隔标记。 使用 '' 清除现有标记。 | 否 |

## <a name="azure-portal"></a>Azure 门户

有关使用 Azure 门户部署 AKS 群集的说明，请参阅 Azure 容器服务 (AKS) [Azure 门户快速入门][aks-portal-quickstart]。 

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md