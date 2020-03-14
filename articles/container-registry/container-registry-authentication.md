---
title: 注册表身份验证选项
description: 专用 Azure 容器注册表的身份验证选项，包括使用 Azure Active Directory 标识登录、使用服务主体和使用可选的管理员凭据。
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247041"
---
# <a name="authenticate-with-an-azure-container-registry"></a>使用 Azure 容器注册表进行身份验证

可通过几种方法使用 Azure 容器注册表进行身份验证，并且每种方法适用于一种或多种注册表使用方案。

推荐的方法包括通过[单个登录名](#individual-login-with-azure-ad)直接对注册表进行身份验证，或者应用程序和容器协调器可以通过使用 Azure Active Directory （Azure AD）[服务主体](#service-principal)执行无人参与或 "无外设" 身份验证。

## <a name="authentication-options"></a>身份验证选项

下表列出了可用的身份验证方法和推荐方案。 有关详细信息，请参阅链接的内容。

| 方法                               | 如何进行身份验证                                           | 方案                                                            | RBAC                             | 限制                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [单个 AD 身份](#individual-login-with-azure-ad)                | Azure CLI  中的 `az acr login`                             | 开发人员、测试人员交互式推送/请求                                    | 是                              | AD 令牌必须每隔3小时续订一次     |
| [AD 服务主体](#service-principal)                  | `docker login`<br/><br/>Azure CLI 中的 `az acr login`<br/><br/> Api 或工具中的注册表登录设置<br/><br/> [Kubernetes 请求机密](container-registry-auth-kubernetes.md)                                           | CI/CD 管道中的无人参与推送<br/><br/> 无人参与拉取到 Azure 或外部服务  | 是                              | SP 密码默认过期时间为1年       |                                                           
| [与 AKS  集成](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                   | AKS 群集创建或更新时附加注册表  | 无人参与拉取到 AKS 群集                                                  | 否，仅请求访问             | 仅适用于 AKS 群集            |
| [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/>Azure CLI  `az acr login`                                        | Azure CI/CD 管道中的无人参与推送<br/><br/> 无人参与请求 Azure 服务<br/><br/>   | 是                              | 仅用于[支持 azure 资源的托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)的 azure 服务              |
| [管理员用户](#admin-account)                            | `docker login`                                          | 单个开发人员或测试人员进行交互式推送/请求                           | 否，始终请求并推送访问  | 每个注册表单个帐户，不建议用于多个用户         |
| [存储库范围的访问令牌](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>Azure CLI 中的 `az acr login`   | 由单个开发人员或测试人员进行交互式推送/拉取到存储库<br/><br/> 按单个系统或外部设备，无人参与推送/拉取到存储库                  | 是                              | 当前未与 AD 标识集成  |

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 进行单次登录

直接使用注册表时（例如向开发工作站拉取映像和从中推送映像），可通过在 [Azure CLI](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 中使用 [az acr login](/cli/azure/install-azure-cli) 命令进行身份验证：

```azurecli
az acr login --name <acrName>
```

使用 `az acr login` 进行登录时，CLI 将使用执行 [az login](/cli/azure/reference-index#az-login) 时创建的令牌和注册表对会话进行无缝身份验证。 若要完成身份验证流，必须在你的环境中安装并运行 Docker。 `az acr login` 使用 Docker 客户端在 `docker.config` 文件中设置 Azure Active Directory 令牌。 以这种方式登录后，系统会缓存凭据，并且会话中的后续 `docker` 命令将不再需要用户名或密码。

> [!TIP]
> 当你希望将 Docker 映像以外的项目推送或拉取到注册表（如[OCI 项目](container-registry-oci-artifacts.md)）时，还可以使用 `az acr login` 来对单个标识进行身份验证。  


对于注册表访问，`az acr login` 使用的令牌有效期为**3 小时**，因此建议你在运行 `docker` 命令之前始终登录到注册表。 如果令牌过期，可以通过再次使用 `az acr login` 命令重新进行身份验证来刷新令牌。 

配合使用 `az acr login` 和 Azure 标识可提供[基于角色的访问](../role-based-access-control/role-assignments-portal.md)。 在某些情况下，你可能想要在 Azure AD 中使用自己的单个标识登录注册表。 对于跨服务方案或处理工作组或开发工作流的需求，而你不希望管理单个访问，你还可以使用[Azure 资源的托管标识](container-registry-authentication-managed-identity.md)登录。

## <a name="service-principal"></a>服务主体

如果为注册表分配了[服务主体](../active-directory/develop/app-objects-and-service-principals.md)，则应用程序或服务可以将其用于无外设身份验证。 服务主体允许通过[基于角色的访问](../role-based-access-control/role-assignments-portal.md)来访问注册表，并且可以为注册表分配多个服务主体。 如果拥有多个服务主体，则可为不同应用程序定义不同的访问权限。

容器注册表的可用角色包括：

* **AcrPull**：拉取

* **AcrPush**：拉取和推送

* **所有者**：拉取、推送和为其他用户分配角色

有关完整的角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。

有关使用 Azure 容器注册表进行身份验证的 CLI 脚本创建服务主体的详细信息，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)。

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

若要启用现有注册表的管理员用户，可以在 Azure CLI 中使用 `--admin-enabled`az acr update[ 命令的 ](/cli/azure/acr?view=azure-cli-latest#az-acr-update) 参数：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

可以在 Azure 门户中启用管理员用户，操作方法如下：导航你的注册表，在“设置”下选择“访问密钥”，然后在“管理员用户”下选择“启用”。

![在 Azure 门户中启用管理员用户的用户界面][auth-portal-01]

## <a name="next-steps"></a>后续步骤

* [使用 Azure CLI 推送第一个映像](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
