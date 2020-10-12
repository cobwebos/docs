---
title: 使用服务主体进行身份验证
description: 使用 Azure Active Directory 服务主体允许访问专用容器注册表中的映像。
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 8d49628576a1c337efaea3e5286fef00e39def17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259151"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服务主体的 Azure 容器注册表身份验证

可以使用 Azure Active Directory (Azure AD) 服务主体提供对容器注册表的容器映像 `docker push` 和 `pull` 访问权限。 通过使用服务主体，可以提供对“无外设”服务和应用程序的访问权限。

## <a name="what-is-a-service-principal"></a>什么是服务主体？

Azure AD“服务主体”  提供对订阅中的 Azure 资源的访问权限。 可以将服务主体视为某个服务的用户标识，其中，“服务”是需要访问资源的任何应用程序、服务或平台。 可以为服务主体配置作用域仅限于你指定的那些资源的访问权限。 然后，将应用程序或服务配置为使用服务主体的凭据来访问这些资源。

在 Azure 容器注册表的上下文中，你可以创建对 Azure 中的专用注册表具有拉取、推送和拉取或其他权限的 Azure AD 服务主体。 有关完整列表，请参阅 [Azure 容器注册表的角色和权限](container-registry-roles.md)。

## <a name="why-use-a-service-principal"></a>为何使用服务主体？

通过使用 Azure AD 服务主体，可以针对专用容器注册表提供具有作用域的访问权限。 可以为每个应用程序或服务创建不同的服务主体，每个服务主体对注册表具有定制的访问权限。 而且，因为你可以避免在各个服务和应用程序之间共享凭据，因此可以仅针对你选择的服务主体（和涉及的应用程序）滚动更新凭据或撤销访问权限。

例如，将 Web 应用程序配置为使用仅为其提供了映像 `pull` 访问权限的服务主体，而生成系统则使用为其提供了 `push` 和 `pull` 访问权限的服务主体。 如果应用程序开发变更了人手，则你可以滚动更新其服务主体凭据且不会影响生成系统。

## <a name="when-to-use-a-service-principal"></a>何时使用服务主体

在**无外设方案**中，应当使用服务主体来提供注册表访问。 即，任何必须以自动或其他无人参与方式来推送或拉取容器映像的应用程序、服务或脚本。 例如：

  * *拉取*：将容器从注册表部署到业务流程系统（包括 Kubernetes、DC/OS 和 Docker Swarm）。 还可以从容器注册表拉取到相关的 Azure 服务，例如 [Azure Kubernetes 服务 (AKS)](../aks/cluster-container-registry-integration.md)、[Azure 容器实例](container-registry-auth-aci.md)、[应用服务](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](../service-fabric/index.yml)，等等。

  * *Push*：构建容器映像并使用持续集成和部署解决方案（例如 Azure Pipelines 或 Jenkins）将它们推送到注册表。

若要对注册表进行个人访问，例如手动将容器映像拉取到开发工作站时，我们建议改用你自己的 [Azure AD 标识](container-registry-authentication.md#individual-login-with-azure-ad)进行注册表访问（例如使用 [az acr login][az-acr-login]）。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>示例脚本

可以在 GitHub 上找到前面的 Azure CLI 示例脚本以及 Azure PowerShell 所对应的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>使用服务主体进行身份验证

你拥有已授予对容器注册表的访问权限的服务主体后，就可以配置其凭据以访问“无外设”服务和应用程序，或者使用 `docker login` 命令输入它们。 使用以下值：

* **用户名** - 服务主体应用程序 ID（也称为*客户端 ID*）
* **密码** - 服务主体密码（也称为*客户端密码*）

每个值都是格式为 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 的 GUID。 

> [!TIP]
> 可通过运行 [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令重新生成服务主体的密码。
>

### <a name="use-credentials-with-azure-services"></a>在 Azure 服务中使用凭据

可以使用通过 Azure 容器注册表进行身份验证的任何 Azure 服务的服务主体凭据。  许多情况下可以使用服务主体凭据来代替注册表的管理员凭据。

例如，使用凭据将 Azure 容器注册表中的映像提取到 [Azure 容器实例](container-registry-auth-aci.md)。

### <a name="use-with-docker-login"></a>在 docker login 中使用

可以使用服务主体运行 `docker login`。 在以下示例中，服务主体应用程序 ID 将传入到环境变量 `$SP_APP_ID` 中，密码将传入到变量 `$SP_PASSWD` 中。 有关管理 Docker 凭据的最佳做法，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令参考。

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

登录后，Docker 将缓存凭据。

### <a name="use-with-certificate"></a>与证书一起使用

如果已将证书添加到服务主体，则可使用基于证书的身份验证登录到 Azure CLI，然后使用 [az acr login][az-acr-login] 命令访问注册表。 使用 CLI 时，使用证书作为机密而不是密码可以提高安全性。 

可以在[创建服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)时创建自签名证书。 也可向现有服务主体添加一个或多个证书。 例如，如果使用本文中的多个脚本中的一个来创建或更新有权通过注册表拉取或推送映像的服务主体，请使用 [az ad sp credential reset][az-ad-sp-credential-reset] 命令来添加证书。

若要使用服务主体和证书[登录到 Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)，证书必须为 PEM 格式且包含私钥。 如果证书未采用所需格式，请使用 `openssl` 之类的工具来转换它。 当你运行 [az login][az-login] 以使用服务主体登录到 CLI 时，另请提供服务主体的应用程序 ID 和 Active Directory 租户 ID。 下面的示例演示这些充当环境变量的值：

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

然后运行 [az acr login][az-acr-login]，通过注册表进行身份验证：

```azurecli
az acr login --name myregistry
```

CLI 使用你运行 `az login` 时创建的令牌，通过注册表对会话进行身份验证。

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure 容器注册表进行身份验证的其他方案，请参阅[身份验证概述](container-registry-authentication.md)。

* 有关使用 Azure 密钥保管库存储和检索容器注册表的服务主体凭据的示例，请参阅[使用 ACR 任务生成和部署容器映像](container-registry-tutorial-quick-task.md)教程。

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
