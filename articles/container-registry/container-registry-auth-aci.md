---
title: 使用 Azure 容器注册表从 Azure 容器实例进行身份验证
description: 了解如何使用 Azure Active Directory 服务主体从 Azure 容器实例允许访问专用容器注册表中的映像。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: daa9c098de0c410bd4033cc62ee911631eb3b634
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>使用 Azure 容器注册表从 Azure 容器实例进行身份验证

可以使用 Azure Active Directory (Azure AD) 服务主体提供对 Azure 容器注册表中专用容器注册表的访问权限。

在本文中，了解如何创建和配置具有注册表*拉取*权限的 Azure AD 服务主体。 然后，启动 Azure 容器实例 (ACI) 中的某个容器，以从专用注册表拉取其映像，并使用服务主体进行身份验证。

## <a name="when-to-use-a-service-principal"></a>何时使用服务主体

在**无外设方案**中（如在自动或以其他无人参与方式创建容器实例的应用程序或服务中），应使用服务主体从 ACI 进行身份验证。

例如，如果你有一个在夜间自动运行的脚本，并创建了一个[基于任务的容器实例](../container-instances/container-instances-restart-policy.md)来处理一些数据，则它可以使用具有“仅拉取”（读者）权限的服务主体对注册表进行身份验证。 然后可以轮换服务主体的凭据或完全撤消其访问权限，而不会影响其他服务和应用程序。

在禁用了注册表[管理员用户](container-registry-authentication.md#admin-account)时，也应使用服务主体。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>使用服务主体进行身份验证

若要使用服务主体启动 Azure 容器实例中的容器，请为 `--registry-username` 指定其 ID，并为 `--registry-password` 指定其密码。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>示例脚本

可以在 GitHub 上找到前面的 Azure CLI 示例脚本以及 Azure PowerShell 所对应的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>后续步骤

以下文章包含有关使用服务主体和 ACR 的其他详细信息：

* [使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* [使用 Azure 容器注册表从 Azure Kubernetes 服务 (AKS) 进行身份验证](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
