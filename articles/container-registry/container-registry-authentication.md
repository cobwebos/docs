---
title: 注册表身份验证选项
description: Azure 容器注册表的身份验证选项，包括使用 Azure Active Directory 标识、使用服务主体以及使用可选的管理凭据进行登录。
ms.topic: article
ms.date: 12/21/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbe77dee4104e3c654aad58db82765733b2c3e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264503"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>使用私有 Docker 容器注册表进行身份验证

可通过几种方法使用 Azure 容器注册表进行身份验证，并且每种方法适用于一种或多种注册表使用方案。

推荐的方法包括通过[单个登录名](#individual-login-with-azure-ad)直接对注册表进行身份验证，或者应用程序和容器协调器可以通过使用 Azure Active Directory （Azure AD）[服务主体](#service-principal)执行无人参与或 "无外设" 身份验证。

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 进行单次登录

直接使用注册表时（例如向开发工作站拉取映像和从中推送映像），可通过在 [Azure CLI](/cli/azure/install-azure-cli) 中使用 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 命令进行身份验证：

```azurecli
az acr login --name <acrName>
```

使用 `az acr login` 进行登录时，CLI 将使用执行 [az login](/cli/azure/reference-index#az-login) 时创建的令牌和注册表对会话进行无缝身份验证。 以这种方式登录后，系统会缓存凭据，并且会话中的后续 `docker` 命令将不再需要用户名或密码。 

对于注册表访问，`az acr login` 使用的令牌有效期为**3 小时**，因此建议你在运行 `docker` 命令之前始终登录到注册表。 如果令牌过期，可以通过再次使用 `az acr login` 命令重新进行身份验证来刷新令牌。 

配合使用 `az acr login` 和 Azure 标识可提供[基于角色的访问](../role-based-access-control/role-assignments-portal.md)。 在某些情况下，你可能想要在 Azure AD 中使用自己的单个标识登录注册表。 对于跨服务方案，或者若要在不想管理个人访问的情况下满足工作组的需求，还可以使用 [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)进行登录。

## <a name="service-principal"></a>服务主体

如果为注册表分配了[服务主体](../active-directory/develop/app-objects-and-service-principals.md)，则应用程序或服务可以将其用于无外设身份验证。 服务主体允许通过[基于角色的访问](../role-based-access-control/role-assignments-portal.md)来访问注册表，并且可以为注册表分配多个服务主体。 如果拥有多个服务主体，则可为不同应用程序定义不同的访问权限。

容器注册表的可用角色包括：

* **AcrPull**：拉取

* **AcrPush**：拉取和推送

* **所有者**：拉取、推送和为其他用户分配角色

有关完整的角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。

有关使用 Azure 容器注册表进行身份验证时要使用的 CLI 脚本创建服务主体，以及使用服务主体的指导，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)。

## <a name="admin-account"></a>管理员帐户

每个容器注册表包含一个管理员用户帐户，此帐户默认禁用。 可以在 Azure 门户中或通过使用 Azure CLI 或其他 Azure 工具启用管理员用户并管理其凭据。

> [!IMPORTANT]
> 管理员帐户专门用于单个用户访问注册表，主要用于测试目的。 我们不建议在多个用户之间共享管理员帐户凭据。 对于使用管理员帐户进行身份验证的所有用户，他们都将显示为对注册表具有推送和拉取访问权限的单个用户。 更改或禁用此帐户会禁用使用凭据的所有用户的注册表访问权限。 建议用户和服务主体在无外设方案中使用单个标识。
>

管理员帐户有两个密码，这两个密码都可以再生成。 使用这两个密码，可以在再生成一个密码时使用另一个密码保持与注册表的连接。 如果管理员帐户已启用，可以在系统提示时将用户名和/或密码传递到 `docker login` 命令，以对注册表进行基本身份验证。 例如：

```
docker login myregistry.azurecr.io 
```

有关管理登录凭据的最佳实践，请参阅[docker login](https://docs.docker.com/engine/reference/commandline/login/)命令参考。

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
