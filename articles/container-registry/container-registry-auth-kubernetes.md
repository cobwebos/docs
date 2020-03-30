---
title: 从库伯奈斯群集进行身份验证
description: 了解如何通过使用服务主体创建拉取密钥，为 Kubernetes 群集提供访问 Azure 容器注册表中图像的访问权限
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154889"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>将映像从 Azure 容器注册表拉至库伯奈斯群集

您可以将 Azure 容器注册表用作任何 Kubernetes 群集的容器映像源，包括"本地"库伯奈斯群集（如[minikube](https://minikube.sigs.k8s.io/)和[类型](https://kind.sigs.k8s.io/)）。 本文演示如何基于 Azure 活动目录服务主体创建 Kubernetes 拉取密钥。 然后，使用 该机密从 Kubernets 部署中的 Azure 容器注册表中提取映像。

> [!TIP]
> 如果使用托管[的 Azure Kubernetes 服务](../aks/intro-kubernetes.md)，还可以将群集与目标 Azure 容器注册表[集成](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)以进行映像提取。 

本文假定您已经创建了专用 Azure 容器注册表。 您还需要运行 Kubernetes 群集并通过`kubectl`命令行工具进行访问。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果不保存或记住服务主体密码，可以使用[az ad sp 凭据重置][az-ad-sp-credential-reset]命令重置密码：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令返回服务主体的新有效密码。

## <a name="create-an-image-pull-secret"></a>创建图像拉取密钥

Kubernetes 使用*映像提取密钥*来存储向注册表进行身份验证所需的信息。 要为 Azure 容器注册表创建拉取密钥，请提供服务主体 ID、密码和注册表 URL。 

使用以下`kubectl`命令创建图像拉取密钥：

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
其中：

| “值” | 描述 |
| :--- | :--- |
| `secret-name` | 图像拉取机密的名称，例如 *，acr-机密* |
| `namespace` | 库伯内斯命名空间，把秘密 <br/> 仅当要将机密放置在默认命名空间以外的命名空间中时，才需要 |
| `container-registry-name` | Azure 容器注册表的名称 |
| `service-principal-ID` | Kubernetes 将用来访问注册表的服务主体的 ID |
| `service-principal-password` | 服务主体密码 |

## <a name="use-the-image-pull-secret"></a>使用图像拉取密码

创建映像拉取密钥后，可以使用它创建 Kubernetes pod 和部署。 在部署文件中提供机密`imagePullSecrets`的名称。 例如：

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

在前面的示例中，`your-awesome-app:v1`是从 Azure 容器注册表中提取的图像的名称，是`acr-secret`您为访问注册表而创建的拉取密钥的名称。 部署 Pod 时，Kubernetes 会自动从注册表中拉出映像（如果群集上尚未存在）。


## <a name="next-steps"></a>后续步骤

* 有关使用服务主体和 Azure 容器注册表，请参阅[具有服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* 在[库伯内斯文档中](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)了解有关图像拉取秘密的更多资料


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset