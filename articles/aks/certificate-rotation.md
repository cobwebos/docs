---
title: 在 Azure Kubernetes Service （AKS）中轮替证书
description: 了解如何在 Azure Kubernetes 服务（AKS）群集中轮替证书。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 774a0354c6262598c7d5e1f51e2e475fd17fe2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468274"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中轮替证书

Azure Kubernetes Service （AKS）使用证书进行身份验证，使其包含许多组件。 出于安全或策略原因，你可能需要定期轮换这些证书。 例如，你可能有一个策略每90天轮换你的所有证书。

本文介绍如何在 AKS 群集中轮替证书。

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 版本2.0.77 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 证书、证书颁发机构和服务帐户

AKS 生成并使用以下证书、证书颁发机构和服务帐户：

* AKS API 服务器将创建一个名为群集 CA 的证书颁发机构（CA）。
* API 服务器有一个群集 CA，它对从 API 服务器到 kubelets 的单向通信的证书进行签名。
* 每个 kubelet 还会创建一个证书签名请求（CSR），该请求由群集 CA 签名，用于从 kubelet 到 API 服务器的通信。
* Etcd 键值存储具有由群集 CA 签名的证书，用于从 etcd 到 API 服务器的通信。
* Etcd 键值存储创建一个 CA，用于对证书进行签名，以便对 AKS 群集中 etcd 副本之间的数据复制进行身份验证和授权。
* API 聚合器使用群集 CA 颁发证书，以便与其他 Api （例如，Azure 的开放 Service Broker）进行通信。 API 聚合器还可以具有其自己的用于颁发这些证书的 CA，但它目前使用群集 CA。
* 每个节点都使用由群集 CA 签名的服务帐户（SA）令牌。
* `kubectl` 客户端具有用于与 AKS 群集通信的证书。

> [!NOTE]
> 2019年3月之前创建的 AKS 群集包含两年后过期的证书。 2019年3月之后创建的任何群集或已旋转证书的任何群集都具有30年后过期的证书。 若要验证群集的创建时间，请使用 `kubectl get nodes` 查看节点池的*使用期限*。
> 
> 此外，还可以检查群集证书的到期日期。 例如，以下命令显示*myAKSCluster*群集的证书详细信息。
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>旋转群集证书

> [!WARNING]
> 使用 `az aks rotate-certs` 轮换证书可能会导致 AKS 群集长达30分钟的停机时间。

使用[az aks get 凭据][az-aks-get-credentials]登录到 aks 群集。 此命令还会在本地计算机上下载并配置 `kubectl` 客户端证书。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

使用 `az aks rotate-certs` 来旋转群集上的所有证书、Ca 和 SAs。

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> 完成 `az aks rotate-certs` 最多可能需要30分钟。 如果命令在完成之前失败，请使用 `az aks show` 验证群集的状态是否为 "*证书轮换*"。 如果群集处于失败状态，请重新运行 `az aks rotate-certs` 以再次轮替证书。

通过运行 `kubectl` 命令来验证旧证书是否不再有效。 由于尚未更新 `kubectl`使用的证书，你将看到错误。  例如：

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

通过运行 `az aks get-credentials`更新 `kubectl` 使用的证书。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

通过运行 `kubectl` 命令验证是否已更新证书，该命令现在会成功。 例如：

```console
kubectl get no
```

## <a name="next-steps"></a>后续步骤

本文介绍如何自动轮替群集的证书、Ca 和 SAs。 有关详细信息，请参阅[Azure Kubernetes Service （AKS）中的群集安全性和升级的最佳实践][aks-best-practices-security-upgrades]。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
