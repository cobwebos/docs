---
title: 为 Azure Kubernetes 服务 (AKS) 群集重置凭据
description: 了解如何更新或重置 Azure Kubernetes 服务（AKS）群集的服务主体或 AAD 应用程序凭据
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096107"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>更新或轮换 Azure Kubernetes Service （AKS）的凭据

默认情况下，使用服务主体创建的 AKS 群集具有为期一年的有效期。 在有效期即将结束时，你可以重置凭据来将服务主体延长额外的一段时间。 作为已定义安全策略的一部分，还可能要更新或轮换凭据。 本文详细介绍如何为 AKS 群集更新这些凭据。

你还可以将[AKS 群集与 Azure Active Directory 集成][aad-integration]，并将其用作群集的身份验证提供程序。 在这种情况下，你将为你的群集、AAD 服务器应用和 AAD 客户端应用创建另外2个标识，你还可以重置这些凭据。 

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.65 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>为 AKS 群集更新或创建新的服务主体

要为 AKS 群集更新凭据时，可以选择：

* 为群集使用的现有服务主体更新凭据，或
* 创建服务主体并更新群集以使用这些新凭据。

### <a name="reset-existing-service-principal-credential"></a>重置现有的服务主体凭据

若要更新现有服务主体的凭据，请使用[az aks show][az-aks-show]命令获取群集的服务主体 ID。 以下示例获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的 ID。 服务主体 ID 设置为一个名为*SP_ID*的变量，以便在其他命令中使用。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

使用包含服务主体 ID 的变量集，现在使用[az ad sp credential reset][az-ad-sp-credential-reset]重置凭据。 下面的示例可让 Azure 平台为服务主体生成新安全密码。 此新安全密码也存储为变量。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

现在继续[更新 AKS 群集，并提供新的服务主体凭据](#update-aks-cluster-with-new-service-principal-credentials)。 若要在 AKS 群集上反映服务主体更改，必须执行此步骤。

### <a name="create-a-new-service-principal"></a>创建新的服务主体

如果在上一部分中选择更新现有服务主体凭据，请跳过此步骤。 继续[更新包含新服务主体凭据的 AKS 群集](#update-aks-cluster-with-new-service-principal-credentials)。

若要创建服务主体，然后更新 AKS 群集以使用这些新凭据，请使用[az ad sp 创建-rbac][az-ad-sp-create]命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

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

现在，使用你自己的[az ad sp create for rbac][az-ad-sp-create]命令中的输出定义服务主体 ID 和客户端密钥的变量，如以下示例中所示。 SP_ID 是 appId，SP_SECRET 是 password：

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

现在继续[更新 AKS 群集，并提供新的服务主体凭据](#update-aks-cluster-with-new-service-principal-credentials)。 若要在 AKS 群集上反映服务主体更改，必须执行此步骤。

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>更新具有新服务主体凭据的 AKS 群集

无论你是选择更新现有服务主体的凭据，还是要创建服务主体，现在都可以使用[az AKS update-凭据][az-aks-update-credentials]命令更新 AKS 群集和新的凭据。 会使用 --service-principal 和 --client-secret 的变量：

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

在 AKS 中更新服务主体凭据需要一段时间。

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>更新具有新 AAD 应用程序凭据的 AKS 群集

可以按照[AAD 集成步骤][create-aad-app]创建新的 AAD 服务器和客户端应用程序。 或重置现有的 AAD 应用程序，[方法与服务主体重置的方法相同](#reset-existing-service-principal-credential)。 之后，只需使用相同的[az aks][az-aks-update-credentials]命令更新群集 AAD 应用程序凭据，但使用 *--reset--AAD*变量即可。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>后续步骤

在本文中，已更新 AKS 群集本身和 AAD 集成应用程序的服务主体。 有关如何在群集中管理工作负荷标识的详细信息，请参阅[AKS 中的身份验证和授权的最佳实践][best-practices-identity]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
