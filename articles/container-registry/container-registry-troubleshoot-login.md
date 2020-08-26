---
title: 排查对注册表的登录问题
description: 登录到 Azure 容器注册表时遇到的常见问题、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 8fbb96be8223001ac52db47788c31609e9b86e35
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227110"
---
# <a name="troubleshoot-registry-login"></a>注册表登录疑难解答

本文将帮助你解决在登录到 Azure 容器注册表时可能会遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 无法使用 `docker login` 、或同时登录到 `az acr login` 注册表
* 无法登录到注册表，出现错误 `unauthorized: authentication required` 或 `unauthorized: Application not registered with AAD`
* 无法登录到注册表，出现 Azure CLI 错误 `Could not connect to the registry login server`
* 无法推送或请求映像，出现 Docker 错误 `unauthorized: authentication required`
* 无法从 Azure Kubernetes 服务、Azure DevOps 或其他 Azure 服务访问注册表
* 无法访问注册表，出现错误 `Error response from daemon: login attempt failed with status: 403 Forbidden` -请参阅 [对注册表的网络问题进行故障排除](container-registry-troubleshoot-access.md)
* 无法使用 Azure CLI 访问或查看 Azure 门户或管理注册表中的注册表设置

## <a name="causes"></a>原因

* 你的环境中的 Docker 配置不正确- [解决方案](#check-docker-configuration)
* 注册表不存在或名称不正确- [解决方案](#specify-correct-registry-name)
* 注册表凭据无效- [解决方案](#confirm-credentials-to-access-registry)
* 凭据未获得推送、请求或 Azure 资源管理器操作的授权- [解决方案](#confirm-credentials-are-authorized-to-access-registry)
* 凭据已过期- [解决方案](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>进一步诊断 

运行 [az acr 检查运行状况](/cli/azure/acr#az-acr-check-health) 命令，以获取有关注册表环境运行状况的详细信息，并可以选择访问目标注册表。 例如，诊断 Docker 配置错误或 Azure Active Directory 登录问题。 

有关命令示例，请参阅 [查看 Azure 容器注册表的运行状况](container-registry-check-health.md) 。 如果报告了错误，请查看 [错误引用](container-registry-health-error-reference.md) 和以下部分，了解建议的解决方法。

> [!NOTE]
> 如果存在阻止注册表访问的防火墙或网络配置，还会发生某些身份验证或授权错误。 请参阅 [对注册表的网络问题进行故障排除](container-registry-troubleshoot-access.md)。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="check-docker-configuration"></a>检查 Docker 配置

大多数 Azure 容器注册表身份验证流需要本地 Docker 安装，因此你可以使用注册表进行身份验证，以便执行推送和提取映像等操作。 确认 Docker CLI 客户端和后台程序 (Docker 引擎) 正在您的环境中运行。 需要 Docker 客户端版本18.03 或更高版本。

相关链接：

* [身份验证概述](container-registry-authentication.md#authentication-options)
* [容器注册表常见问题](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>指定正确的注册表名称

使用时 `docker login` ，请提供注册表的完整登录服务器名称，例如 *myregistry.azurecr.io*。 请确保只使用小写字母。 例如：

```console
docker login myregistry.azurecr.io
```

使用带有 Azure Active Directory 标识的 [az acr 登录名](/cli/azure/acr#az-acr-login) 时，请先 [登录到 Azure CLI](/cli/azure/authenticate-azure-cli)，然后指定注册表的 Azure 资源名称。 资源名称是创建注册表时所提供的名称，例如 *myregistry* (没有) 的域后缀。 例如：

```azurecli
az acr login --name myregistry
```

相关链接：

* [az acr login 成功，但 Docker 失败并出现错误：未授权: 需要身份验证](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>确认访问注册表的凭据

检查用于方案的凭据的有效性，或由注册表所有者提供给你。 一些可能的问题：

* 如果使用 Active Directory 服务主体，请确保在 Active Directory 租户中使用正确的凭据：
  * 用户名 - 服务主体应用程序 ID（也称为*客户端 ID*）
  * 密码 - 服务主体密码（也称为*客户端密码*）
* 如果使用 azure 服务（如 Azure Kubernetes 服务或 Azure DevOps）来访问注册表，请确认服务的注册表配置。
* 如果 `az acr login` 使用 `--expose-token` 选项运行，而无需使用 Docker 守护程序即可启用注册表登录，请确保使用用户名进行身份验证 `00000000-0000-0000-0000-000000000000` 。

相关链接：

* [身份验证概述](container-registry-authentication.md#authentication-options)
* [使用 Azure AD 进行单次登录](container-registry-authentication.md#individual-login-with-azure-ad)
* [用服务主体登录](container-registry-auth-service-principal.md)
* [具有托管标识的登录名](container-registry-authentication-managed-identity.md)
* [用存储库范围内的令牌登录](container-registry-repository-scoped-permissions.md)
* [用管理员帐户登录](container-registry-authentication.md#admin-account)
* [Azure AD 身份验证和授权错误代码](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az-acr-login) 引用

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>确认授权访问注册表的凭据

确认与凭据关联的注册表权限，如 `AcrPull` 用于从注册表中提取映像的 RBAC 角色或 `AcrPush` 用于推送映像的角色。 

若要在门户中访问注册表或使用 Azure CLI 进行注册表管理，至少需要 `Reader` 执行 Azure 资源管理器操作的角色。

你或注册表所有者必须在订阅中具有足够的权限，才能添加或删除角色分配。

相关链接：

* [RBAC 角色和权限-Azure 容器注册表](container-registry-roles.md)
* [用存储库范围内的令牌登录](container-registry-repository-scoped-permissions.md)
* [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)
* [使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)
* [创建新的应用程序机密](../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)
* [Azure AD 身份验证和授权代码](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>检查凭据是否未过期

令牌和 Active Directory 凭据可能会在定义的时间段后过期，从而阻止注册表访问。 若要启用访问权限，可能需要重置或重新生成凭据。

* 如果使用单个 AD 标识、托管标识或注册登录的服务主体，则 AD 令牌将在3小时后过期。 再次登录到注册表。  
* 如果使用的 AD 服务主体的客户端密码已过期，则订阅所有者或帐户管理员需要重置凭据或生成新的服务主体。
* 如果使用 [存储库范围的令牌](container-registry-repository-scoped-permissions.md)，则注册表所有者可能需要重置密码或生成新令牌。

相关链接：

* [重置服务主体凭据](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [重新生成令牌密码](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [使用 Azure AD 进行单次登录](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>高级故障排除

如果在注册表中启用了 [资源日志收集](container-registry-diagnostics-audit-logs.md) ，请查看 ContainterRegistryLoginEvents 日志。 此日志存储身份验证事件和状态，包括传入的标识和 IP 地址。 查询日志中的 [注册表身份验证失败](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)。 

相关链接：

* [诊断评估和审核日志](container-registry-diagnostics-audit-logs.md)
* [容器注册表常见问题](container-registry-faq.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果未在此处解决问题，请参阅以下选项。

* 其他注册表疑难解答主题包括：
  * [对注册表中的网络问题进行故障排除](container-registry-troubleshoot-access.md)
  * [注册表性能疑难解答](container-registry-troubleshoot-performance.md)
* [社区支持](https://azure.microsoft.com/support/community/) 选项
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* 根据你提供的信息[打开支持票证](https://azure.microsoft.com/support/create-ticket/)，你可能会在注册表中运行一个快速诊断来验证身份验证失败


