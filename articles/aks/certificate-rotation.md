---
title: 轮换 Azure Kubernetes 服务 (AKS) 中的证书
description: 了解如何轮换 Azure Kubernetes 服务 (AKS) 群集中的证书。
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 90526b78e65c335f07a2a9d2d152b54b47233082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211033"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>轮换 Azure Kubernetes 服务 (AKS) 中的证书

Azure Kubernetes 服务 (AKS) 使用证书对其许多组件进行身份验证。 出于安全或策略原因，可能需要定期轮换这些证书。 例如，某个策略可能每 90 天轮换一次所有证书。

本文介绍如何轮换 AKS 群集中的证书。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.77 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 证书、证书颁发机构和服务帐户

AKS 生成并使用以下证书、证书颁发机构和服务帐户：

* AKS API 服务器创建一个称为群集 CA 的证书颁发机构 (CA)。
* API 服务器具有一个群集 CA，该 CA 对证书进行签名，以用于从 API 服务器到 kubelet 的单向通信。
* 每个 kubelet 还创建一个证书签名请求 (CSR)，该 CSR 由群集 CA 签名，用于从 kubelet 到 API 服务器的通信。
* etcd 键值存储具有由群集 CA 签名的证书，用于从 etcd 到 API 服务器的通信。
* etcd 键值存储创建一个 CA，该 CA 对证书进行签名，以便对 AKS 群集中 etcd 副本之间的数据复制进行身份验证和授权。
* API 聚合器使用群集 CA 颁发证书，以便与其他 API 进行通信。 API 聚合器也可以拥有自己的 CA 来颁发这些证书，但它目前使用群集 CA。
* 每个节点都使用服务帐户 (SA) 令牌，该令牌由群集 CA 签名。
* `kubectl` 客户端具有用于与 AKS 群集通信的证书。

> [!NOTE]
> 对于 2019 年 3 月之前创建的 AKS 群集，其证书将在两年后到期。 任何在 2019 年 3 月之后创建的群集或其证书已轮换的群集都具有 30 年后过期的群集 CA 证书。 其他所有证书均两年后过期。 若要验证群集的创建时间，请使用 `kubectl get nodes` 查看节点池的*存在时间*。
> 
> 此外，还可以检查群集证书的到期日期。 例如，以下 Bash 命令会显示 myAKSCluster 群集的证书详细信息。
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>轮换群集证书

> [!WARNING]
> 使用 `az aks rotate-certs` 轮换证书可能会导致 AKS 群集最多停机 30 分钟。

使用 [az aks get-credentials][az-aks-get-credentials] 登录到 AKS 群集。 此命令还会在本地计算机上下载并配置 `kubectl` 客户端证书。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

使用 `az aks rotate-certs` 轮换群集上的所有证书、CA 和 SA。

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> `az aks rotate-certs` 最多可能需要 30 分钟才能完成。 如果该命令还未完成就已失败，请使用 `az aks show` 验证群集的状态是否为“正在轮换证书”  。 如果群集处于失败状态，请重新运行 `az aks rotate-certs` 以再次轮换证书。

通过运行 `kubectl` 命令来验证旧证书是否不再有效。 由于尚未更新 `kubectl` 使用的证书，因此将显示一个错误。  例如：

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

通过运行 `az aks get-credentials` 来更新 `kubectl` 使用的证书。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

通过运行 `kubectl` 命令来验证证书是否已更新，该命令现在将成功。 例如：

```console
kubectl get no
```

> [!NOTE]
> 如果有任何在 AKS 上运行的服务（如 [Azure Dev Spaces][dev-spaces]），则可能还需要更新与 [这些服务相关的证书][dev-spaces-rotate] 。

## <a name="next-steps"></a>后续步骤

本文介绍了如何自动轮换群集的证书、CA 和 SA。 有关 AKS 安全最佳做法的详细信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中的群集安全性和升级的最佳做法][aks-best-practices-security-upgrades]。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
