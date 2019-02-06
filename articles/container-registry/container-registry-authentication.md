---
title: 使用 Azure 容器注册表进行身份验证
description: Azure 容器注册表的身份验证选项，包括使用 Azure Active Directory 标识、使用服务主体以及使用可选的管理凭据进行登录。
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66f9c41e2551dffc32932f1cfa53fa444251b303
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301044"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>使用私有 Docker 容器注册表进行身份验证

可通过几种方法使用 Azure 容器注册表进行身份验证，并且每种方法适用于一种或多种注册表使用方案。

你可以通过[个人登录](#individual-login-with-azure-ad)来直接登录到注册表，应用程序和容器业务流程协调程序也可以通过使用 Azure Active Directory (Azure AD) [服务主体](#service-principal)执行无人参与或“无外设”身份验证。

Azure 容器注册表不支持未经身份验证的 Docker 操作或匿名访问。 对于公共映像，可以使用 [Docker 中心](https://docs.docker.com/docker-hub/)。

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 进行单次登录

直接使用注册表时（例如向开发工作站拉取映像和从中推送映像），可通过在 [Azure CLI](/cli/azure/install-azure-cli) 中使用 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 命令进行身份验证：

```azurecli
az acr login --name <acrName>
```

使用 `az acr login` 进行登录时，CLI 将使用执行 [az login](/cli/azure/reference-index#az-login) 时创建的令牌和注册表对会话进行无缝身份验证。 以这种方式登录后，系统会缓存凭据，并且会话中的后续 `docker` 命令将不再需要用户名或密码。 

对于注册表访问，`az acr login` 使用的令牌有效期为 1 小时，因此，建议在运行 `docker` 命令之前始终登录到注册表。 如果令牌过期，可以通过再次使用 `az acr login` 命令重新进行身份验证来刷新令牌。 

配合使用 `az acr login` 和 Azure 标识可提供[基于角色的访问](../role-based-access-control/role-assignments-portal.md)。 在某些情况下，你可能希望使用 Azure AD 中你自己的个人标识登录注册表。 对于跨服务方案，或者若要在不想管理个人访问的情况下满足工作组的需求，还可以使用 [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)进行登录。

## <a name="service-principal"></a>服务主体

如果为注册表分配了[服务主体](../active-directory/develop/app-objects-and-service-principals.md)，则应用程序或服务可以将其用于无外设身份验证。 服务主体允许通过[基于角色的访问](../role-based-access-control/role-assignments-portal.md)来访问注册表，并且可以为注册表分配多个服务主体。 如果拥有多个服务主体，则可为不同应用程序定义不同的访问权限。

容器注册表的可用角色包括：

* **AcrPull**：拉取

* **AcrPush**：拉取和推送

* **所有者**：拉取、推送和为其他用户分配角色

有关完整的角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。

如果 CLI 脚本创建服务主体应用 ID 和密码来向 Azure 容器注册表进行身份验证，或者要使用现有的服务主体，请参阅[使用服务主体进行 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)。

服务主体在拉取和推送方案中启用到注册表的“无外设”连接，如下所示：

  * *拉取*：将容器从注册表部署到业务流程系统（包括 Kubernetes、DC/OS 和 Docker Swarm）。 还可以从容器注册表拉取到相关的 Azure 服务，例如 [Azure Kubernetes 服务](container-registry-auth-aks.md)、[Azure 容器实例](container-registry-auth-aci.md)、[应用服务](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](/azure/service-fabric/)，等等。

  * *推送*：构建容器映像并使用持续集成和部署解决方案（例如 Azure Pipelines 或 Jenkins）将它们推送到注册表。

还可以直接使用服务主体登录。 运行以下命令时，收到提示后，请以交互方式提供服务主体 appID（用户名）和密码。 有关管理登录凭据的最佳做法，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令参考：

```Docker
docker login myregistry.azurecr.io
```

登录后，Docker 会缓存凭据，因此无需记住应用 ID。

> [!TIP]
> 可通过运行 [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) 命令重新生成服务主体的密码。
>

## <a name="admin-account"></a>管理员帐户

每个容器注册表包含一个管理员用户帐户，此帐户默认禁用。 可以在 Azure 门户中或通过使用 Azure CLI 或其他 Azure 工具启用管理员用户并管理其凭据。

> [!IMPORTANT]
> 管理员帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 对于使用管理员帐户进行身份验证的所有用户，他们都将显示为对注册表具有推送和拉取访问权限的单个用户。 更改或禁用此帐户会禁用使用凭据的所有用户的注册表访问权限。 建议用户和服务主体在无外设方案中使用单个标识。
>

管理员帐户有两个密码，这两个密码都可以再生成。 使用这两个密码，可以在再生成一个密码时使用另一个密码保持与注册表的连接。 如果管理员帐户已启用，可以在系统提示时将用户名和/或密码传递到 `docker login` 命令，以对注册表进行基本身份验证。 例如：

```Docker
docker login myregistry.azurecr.io 
```


若要启用现有注册表的管理员用户，可以在 Azure CLI 中使用 [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) 命令的 `--admin-enabled` 参数：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

可以在 Azure 门户中启用管理员用户，操作方法如下：导航你的注册表，在“设置”下选择“访问密钥”，然后在“管理员用户”下选择“启用”。

![在 Azure 门户中启用管理员用户的用户界面][auth-portal-01]

## <a name="next-steps"></a>后续步骤

* [使用 Azure CLI 推送第一个映像](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
