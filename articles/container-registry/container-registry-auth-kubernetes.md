---
title: 从 Kubernetes 群集进行身份验证
description: 了解如何通过使用服务主体创建拉取机密来为 Kubernetes 群集提供对 Azure 容器注册表中映像的访问权限。
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84309809"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>将映像从 Azure 容器注册表拉取到 Kubernetes 群集

你可以将 Azure 容器注册表用作容器映像的来源，并将其用于任何 Kubernetes 群集，包括“本地”Kubernetes 群集，例如 [minikube](https://minikube.sigs.k8s.io/) 和 [kind](https://kind.sigs.k8s.io/)。 本文展示了如何基于 Azure Active Directory 服务主体创建 Kubernetes 拉取机密。 然后，在 Kubernetes 部署中使用该机密从 Azure 容器注册表中拉取映像。

> [!TIP]
> 如果你使用托管 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)，则还可以[将群集与目标 Azure 容器注册表集成](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)以用于映像拉取。 

本文假设你已创建了一个专用 Azure 容器注册表。 你还需要确保 Kubernetes 群集正在运行并可通过 `kubectl` 命令行工具访问。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果你未保存或记住服务主体密码，则可以使用 [az ad sp credential reset][az-ad-sp-credential-reset] 命令重置密码：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令为你的服务主体返回一个新的有效密码。

## <a name="create-an-image-pull-secret"></a>创建映像拉取机密

Kubernetes 使用“映像拉取机密”  来存储向注册表证明身份所需的信息。 若要为 Azure 容器注册表创建拉取机密，请提供服务主体 ID、密码和注册表 URL。 

使用以下 `kubectl` 命令创建映像拉取机密：

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
其中：

| 值 | 说明 |
| :--- | :--- |
| `secret-name` | 映像拉取机密的名称，例如 *acr-secret* |
| `namespace` | 用来放置机密的 Kubernetes 命名空间 <br/> 仅当要将机密置于默认命名空间之外的命名空间中时才需要此项 |
| `container-registry-name` | Azure 容器注册表的名称，例如 myregistry<br/><br/>`--docker-server` 是注册表登录服务器的完全限定名称  |
| `service-principal-ID` | Kubernetes 用来访问注册表的服务主体的 ID |
| `service-principal-password` | 服务主体密码 |

## <a name="use-the-image-pull-secret"></a>使用映像拉取机密

创建映像拉取机密后，可以使用它来创建 Kubernetes Pod 和部署。 在部署文件中，在 `imagePullSecrets` 下提供机密名称。 例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

在前面的示例中，`my-awesome-app:v1` 是要从 Azure 容器注册表中拉取的映像的名称，`acr-secret` 是你创建的用于访问注册表的拉取密钥的名称。 部署 Pod 时，如果群集上尚无映像，则 Kubernetes 会自动从注册表中拉取映像。


## <a name="next-steps"></a>后续步骤

* 若要详细了解如何使用服务主体和 Azure 容器注册表，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中详细了解映像拉取机密


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset