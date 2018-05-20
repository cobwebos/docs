---
title: Azure Kubernetes 群集的服务主体
description: 在 AKS 中为 Kubernetes 群集创建和管理 Azure Active Directory 服务主体
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5e6e24d132598e3f79e4fe76c13ee0ae6a82424d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 的服务主体

AKS 群集需要 [Azure Active Directory 服务主体][aad-service-principal]才能与 Azure API 交互。 需要服务主体才能动态创建和管理资源，例如 [Azure 负载均衡器][azure-load-balancer-overview]。

本文介绍用于在 AKS 中为 Kubernetes 群集设置服务主体的不同选项。

## <a name="before-you-begin"></a>开始之前


若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者为 Kubernetes 群集预先创建一个服务主体。

还需安装并配置 Azure CLI 2.0.27 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-sp-with-aks-cluster"></a>使用 AKS 群集创建 SP

使用 `az aks create` 命令部署 AKS 群集时，可以选择自动生成服务主体。

在以下示例中，已经创建了 AKS 群集，但由于未指定现有的服务主体，因此为群集创建了一个服务主体。 若要完成此操作，帐户必须具有创建服务主体所需的相应权限。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>使用现有的 SP

可以使用现有的 Azure AD 服务主体，也可以预先创建一个适用于 AKS 群集的服务主体。 从 Azure 门户部署群集时，这很有用，因为需提供服务主体信息。 使用现有的服务主体时，必须将客户端机密配置为密码。

## <a name="pre-create-a-new-sp"></a>预先创建新的 SP

若要通过 Azure CLI 来创建服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。

```azurecli
az ad sp create-for-rbac --skip-assignment
```

输出如下所示。 记下 `appId` 和 `password`。 创建 AKS 群集时，将使用这些值。

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>使用现有的 SP

使用预先创建的服务主体时，请提供 `appId` 和 `password` 作为 `az aks create` 命令的参数值。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

如果使用 Azure 门户来部署 AKS 群集，请在 AKS 群集配置窗体的“服务主体客户端 ID”字段中输入 `appId` 值，在“服务主体客户端机密”字段中输入 `password` 值。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>其他注意事项

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

* Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
* 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 `https://www.contoso.org/example`）相关联。 应用程序的 URL 不一定是实际的终结点。
* 指定服务主体的“客户端 ID”时，可以使用 `appId` 的值（如本文所示）或相应的服务主体 `name`（例如，`https://www.contoso.org/example`）。
* 在 Kubernetes 群集的主 VM 和节点 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中。
* 使用 `az aks create` 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 `~/.azure/aksServicePrincipal.json` 文件中。
* 删除由 `az aks create` 创建的 AKS 群集时，不会删除自动创建的服务主体。 使用 `az ad sp delete --id $clientID` 将其删除。

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 服务主体的详细信息，请参阅 Azure AD 应用程序文档。

> [!div class="nextstepaction"]
> [应用程序对象和服务主体对象][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md