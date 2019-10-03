---
title: 为 Azure Kubernetes 服务 (AKS) 群集重置凭据
description: 了解如何为 Azure Kubernetes 服务 (AKS) 中的群集更新或重置服务主体凭据
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 5aac941133296d2040d5dd670155b80f5807e1e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614129"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>为 Azure Kubernetes 服务 (AKS) 中的服务主体更新或轮换凭据

默认情况下，使用服务主体创建的 AKS 群集具有为期一年的有效期。 在有效期即将结束时，你可以重置凭据来将服务主体延长额外的一段时间。 作为已定义安全策略的一部分，还可能要更新或轮换凭据。 本文详细介绍如何为 AKS 群集更新这些凭据。

## <a name="before-you-begin"></a>开始之前

你需要 Azure CLI 版本 2.0.65 或更高版本安装和配置。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="choose-to-update-or-create-a-service-principal"></a>选择要更新或创建服务主体

要为 AKS 群集更新凭据时，可以选择：

* 为群集使用的现有服务主体更新凭据，或
* 创建服务主体并更新群集以使用这些新凭据。

如果要创建服务主体，然后更新 AKS 群集，请跳过此部分中的其余步骤并继续浏览[创建服务主体](#create-a-service-principal)。 如果要为 AKS 群集使用的现有服务主体更新凭据，请继续执行此部分中的步骤。

### <a name="get-the-service-principal-id"></a>获取服务主体 ID

若要更新现有服务主体的凭据，获取群集使用的服务主体 ID [az aks 显示][az-aks-show]命令。 以下示例获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的 ID   。 服务主体 ID 设置为一个名为变量*SP_ID*以便在其他命令中使用。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>更新服务主体凭据

使用包含服务主体 ID 的变量集，现在重置使用的凭据[az ad sp 凭据重置][az-ad-sp-credential-reset]。 下面的示例可让 Azure 平台为服务主体生成新安全密码。 此新安全密码也存储为变量。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

现在继续浏览[使用新凭据更新 AKS 群集](#update-aks-cluster-with-new-credentials)。

## <a name="create-a-service-principal"></a>创建服务主体

如果在上一部分中选择更新现有服务主体凭据，请跳过此步骤。 继续浏览[使用新凭据更新 AKS 群集](#update-aks-cluster-with-new-credentials)。

若要创建服务主体，然后更新在 AKS 群集中使用这些新凭据，请使用[az ad sp 创建-对于-rbac][az-ad-sp-create]命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 下一步会使用这些值。

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

现在定义的服务主体 ID 和客户端使用的机密从你自己的输出变量[az ad sp 创建-对于-rbac][az-ad-sp-create]命令，如下面的示例中所示。 SP_ID  是 appId  ，SP_SECRET  是 password  ：

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>使用新凭据更新 AKS 群集

无论是否选择了更新的现有服务主体的凭据或创建服务主体，您现在更新 AKS 群集与你使用的新凭据[az aks 更新凭据][az-aks-update-credentials]命令。 会使用 --service-principal  和 --client-secret  的变量：

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

在 AKS 中更新服务主体凭据需要一段时间。

## <a name="next-steps"></a>后续步骤

在本文中，更新了 AKS 群集本身的服务主体。 有关如何管理标识为群集内的工作负荷的详细信息，请参阅[身份验证和授权在 AKS 中的最佳做法][best-practices-identity]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
