---
title: 从 Kubernetes 群集进行身份验证
description: 了解如何通过使用服务主体创建请求机密来向 Kubernetes 群集提供对 Azure 容器注册表中的映像的访问权限
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154889"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>将 Azure 容器注册表中的映像提取到 Kubernetes 群集

可以将 Azure 容器注册表用作包含任何 Kubernetes 群集的容器映像源，包括 "local" Kubernetes 群集，例如[minikube](https://minikube.sigs.k8s.io/)和[kind](https://kind.sigs.k8s.io/)。 本文介绍如何根据 Azure Active Directory 服务主体创建 Kubernetes 请求机密。 然后，在 Kubernetes 部署中使用机密从 Azure 容器注册表中提取映像。

> [!TIP]
> 如果使用的是托管[Azure Kubernetes 服务](../aks/intro-kubernetes.md)，则还可以将[群集](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)与用于图像拉的目标 Azure 容器注册表集成。 

本文假设已创建了私有 Azure 容器注册表。 还需要 Kubernetes 群集运行，并可通过 `kubectl` 命令行工具访问。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果不保存或记住服务主体密码，可以用[az ad sp credential reset][az-ad-sp-credential-reset]命令重置它：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令为你的服务主体返回一个新的有效密码。

## <a name="create-an-image-pull-secret"></a>创建映像请求机密

Kubernetes 使用*图像请求机密*来存储对注册表进行身份验证所需的信息。 若要为 Azure 容器注册表创建请求机密，请提供服务主体 ID、密码和注册表 URL。 

使用以下 `kubectl` 命令创建映像请求机密：

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
其中：

| 值 | 说明 |
| :--- | :--- |
| `secret-name` | 图像请求机密的名称，例如， *acr-密码* |
| `namespace` | 要放入机密的 Kubernetes 命名空间 <br/> 仅当要将机密置于默认命名空间之外的命名空间中时需要 |
| `container-registry-name` | Azure 容器注册表的名称 |
| `service-principal-ID` | Kubernetes 用于访问注册表的服务主体的 ID |
| `service-principal-password` | 服务主体密码 |

## <a name="use-the-image-pull-secret"></a>使用映像请求机密

创建映像请求机密后，可以使用它来创建 Kubernetes pod 和部署。 在部署文件中 `imagePullSecrets` 下提供机密的名称。 例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

在前面的示例中，`your-awesome-app:v1` 是要从 Azure 容器注册表中请求的映像的名称，`acr-secret` 是你创建的用于访问注册表的请求密钥的名称。 部署 pod 时，Kubernetes 会自动从注册表中提取映像（如果它在群集上还没有）。


## <a name="next-steps"></a>后续步骤

* 有关使用服务主体和 Azure 容器注册表的详细信息，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* 在[Kubernetes 文档](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中了解有关图像请求机密的详细信息


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset