---
title: 运行状况检查的错误引用
description: 在 Azure 容器注册表中运行 az acr check-health 命令时出现的问题的错误代码及可能的解决方法
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: a23b95ea0eaffc053c47b70107c95d2b1cdc0645
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978308"
---
# <a name="health-check-error-reference"></a>运行状况检查错误参考

下面是有关 [az acr check-health][az-acr-check-health] 命令返回的错误代码的详细信息。 其中针对每个错误列出了可能的解决方法。

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

此错误表示找不到 CLI 的 Docker 客户端。 因此，以下附加检查未运行：查找 Docker 版本、评估 Docker 守护程序状态，以及运行 Docker pull 命令。

*可能的解决方法*：安装 Docker 客户端；将 Docker 路径添加到系统变量。

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

此错误表示 Docker 守护程序状态未提供，或者无法使用 CLI 访问状态。 因此，无法通过 CLI 使用 Docker 操作（例如 `docker login` 和 `docker pull`）。

*可能的解决方法*：重启 Docker 守护程序，或验证其是否已正确安装。

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

此错误表示 CLI 无法运行命令 `docker --version`。

*可能的解决方法*：尝试手动运行该命令，确保安装最新的 CLI 版本，并调查错误消息。

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

此错误表示 CLI 无法将示例映像提取到环境中。

*可能的解决方法*：验证提取映像所需的所有组件是否正常运行。

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

此错误表示 CLI 找不到 Helm 客户端，导致其他 Helm 操作无法运行。

*可能的解决方法*：验证是否已安装 Helm 客户端，并验证其路径是否已添加到系统环境变量。

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

此错误表示 CLI 无法确定安装的 Helm 版本。 如果使用的 Azure CLI 版本（或 Helm 版本）已过时，则可能会发生这种情况。

*可能的解决方法*：更新到最新的 Azure CLI 版本或建议的 Helm 版本；手动运行命令并调查错误消息。

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

此错误表示已对给定注册表登录服务器的 DNS 执行 ping 操作，但未收到响应。这意味着该服务器不可用。 这可能表示出现了某些连接问题。 或者，注册表可能不存在、用户可能对注册表没有权限（因此无法正常检索其登录服务器），或者目标注册表所在的云与 Azure CLI 中使用的云不同。

*可能的解决方法*：验证连接；验证注册表的拼写，并验证注册表是否存在；验证用户是否对注册表拥有适当的权限，以及注册表所在的云是否与 Azure CLI 中使用的云相同。

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

此错误表示给定注册表的质询终结点响应了“403 禁止”HTTP 状态。 此错误表示用户无权访问注册表，很可能是因为存在虚拟网络配置，或者是因为不允许访问注册表的公共终结点。 若要查看当前配置的防火墙规则，请运行 `az acr show --query networkRuleSet --name <registry>`。

*可能的解决方法*：删除虚拟网络规则，或将当前的客户端 IP 地址添加到允许列表。

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

此错误表示目标注册表的质询终结点未发出质询。

*可能的解决方法*：请在一段时间后重试。 如果该错误持续出现，请通过 https://aka.ms/acr/issues 报告问题。

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此错误表示目标注册表的质询终结点发出了质询，但注册表不支持 Azure Active Directory 身份验证。

*可能的解决方法*：尝试使用不同的身份验证方法，例如，使用管理员凭据。 如果用户需要使用 Azure Active Directory 进行身份验证，请通过 https://aka.ms/acr/issues 报告问题。

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此错误表示注册表登录服务器未使用刷新令牌做出响应，因此被拒绝访问目标注册表。 如果用户对注册表没有适当的权限，或者 Azure CLI 的用户凭据已过时，则可能会发生此错误。

*可能的解决方法*：验证用户是否对注册表拥有适当的权限；运行 `az login` 以刷新权限、令牌和凭据。

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此错误表示注册表登录服务器未使用访问令牌做出响应，因此被拒绝访问目标注册表。 如果用户对注册表没有适当的权限，或者 Azure CLI 的用户凭据已过时，则可能会发生此错误。

*可能的解决方法*：验证用户是否对注册表拥有适当的权限；运行 `az login` 以刷新权限、令牌和凭据。

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

此错误表示客户端无法与容器注册表建立安全连接。 如果运行或使用代理服务器，则往往会发生此错误。

*可能的解决方法*：可[在此处](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)找到有关如何使用代理的详细信息。

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

此错误表示 CLI 找不到给定注册表的登录服务器，并且找不到当前云的任何默认后缀。 如果注册表不存在、用户对注册表没有适当的权限、注册表所在的云与当前 Azure CLI 云不匹配，或者 Azure CLI 版本已过时，则可能会发生此错误。

*可能的解决方法*：验证拼写是否正确，注册表是否存在；验证用户是否对注册表拥有适当的权限，注册表和 CLI 环境的云是否匹配；将 Azure CLI 更新到最新版本。

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

此错误表示 CLI 与当前安装的 Docker/公证人版本不兼容。 尝试将公证人版本降级为早于0.6.0 的版本，方法是手动替换 Docker 安装的公证人客户端以解决此问题。

## <a name="next-steps"></a>后续步骤

有关用于检查注册表运行状况的选项，请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

参阅[常见问题解答](container-registry-faq.md)，其中解答了有关 Azure 容器注册表的常见问题和其他已知问题。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
