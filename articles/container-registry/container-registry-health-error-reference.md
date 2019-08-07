---
title: 运行状况检查的错误引用-Azure 容器注册表
description: 通过运行 Azure 容器注册表中的 az acr 检查运行状况诊断命令发现的问题的错误代码和可能的解决方案
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3bb81f33823fff5fb47f5bbbf6bb9b798b26d8af
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840368"
---
# <a name="health-check-error-reference"></a>运行状况检查错误引用

下面是由[az acr 检查运行状况][az-acr-check-health]命令返回的错误代码的详细信息。 对于每个错误, 都列出了可能的解决方案。

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

此错误表示找不到适用于 CLI 的 Docker 客户端。 因此, 不会运行以下附加检查: 查找 Docker 版本、评估 Docker 后台程序状态和运行 Docker pull 命令。

*可能的解决方案*:安装 Docker 客户端;将 Docker 路径添加到系统变量。

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

此错误表示 Docker 守护程序状态不可用, 或者无法使用 CLI 访问它。 因此, 可以通过 CLI 使用 Docker 操作 ( `docker login`如`docker pull`和)。

*可能的解决方案*:重新启动 Docker 守护程序, 或验证是否已正确安装。

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

此错误表示 CLI 无法运行该命令`docker --version`。

*可能的解决方案*:请尝试手动运行命令, 确保安装了最新的 CLI 版本, 并调查错误消息。

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

此错误表示 CLI 无法将示例映像提取到您的环境。

*可能的解决方案*:验证拉取映像所需的所有组件是否正常运行。

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

此错误表示 CLI 找不到 Helm 客户端, 这会排除其他 Helm 操作。

*可能的解决方案*:验证是否已安装 Helm 客户端, 并验证其路径是否已添加到系统环境变量中。

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

此错误表示 CLI 无法确定安装的 Helm 版本。 如果正在使用的 Azure CLI 版本 (或 Helm 版本) 已过时, 则会发生这种情况。

*可能的解决方案*:更新到最新 Azure CLI 版本或建议的 Helm 版本;手动运行命令并调查错误消息。

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

此错误表示已对给定注册表登录服务器的 DNS 进行 ping 操作, 但没有响应, 这意味着它不可用。 这可能表示存在一些连接问题。 或者, 注册表可能不存在, 用户可能没有注册表的权限 (若要正确检索其登录服务器), 或者目标注册表与 Azure CLI 中使用的云不同。

*可能的解决方案*:验证连接;验证注册表是否拼写正确, 以及注册表是否存在;验证用户是否具有正确的权限, 以及注册表的云是否与 Azure CLI 中使用的相同。

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

此错误表示给定注册表的质询终结点响应403禁止的 HTTP 状态。 此错误表示用户无权访问注册表, 最有可能的原因是虚拟网络配置。 若要查看当前配置的防火墙规则, `az acr show --query networkRuleSet --name <registry>`请运行。

*可能的解决方案*:删除虚拟网络规则, 或将当前客户端 IP 地址添加到允许列表。

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

此错误表示目标注册表的质询终结点未发出质询。

*可能的解决方案*:请稍后重试。 如果错误仍然存在, 请在上 https://aka.ms/acr/issues 打开问题。

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此错误表示目标注册表的质询终结点发出质询, 但注册表不支持 Azure Active Directory 身份验证。

*可能的解决方案*:尝试使用不同的身份验证方法, 例如, 使用管理员凭据进行身份验证。 如果用户需要使用 Azure Active Directory 进行身份验证, 请在上 https://aka.ms/acr/issues 打开问题。

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此错误表示注册表登录服务器没有使用刷新令牌进行响应, 因此无法访问目标注册表。 如果用户对注册表没有正确的权限, 或者 Azure CLI 的用户凭据过时, 则会发生此错误。

*可能的解决方案*:验证用户对注册表是否具有适当的权限;运行`az login`可刷新权限、令牌和凭据。

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此错误表示注册表登录服务器没有使用访问令牌进行响应, 因此拒绝访问目标注册表。 如果用户对注册表没有正确的权限, 或者 Azure CLI 的用户凭据过时, 则会发生此错误。

*可能的解决方案*:验证用户对注册表是否具有适当的权限;运行`az login`可刷新权限、令牌和凭据。

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

此错误表示客户端无法建立到容器注册表的安全连接。 如果你正在运行或使用代理服务器, 通常会发生此错误。

*可能的解决方案*:可在[此处找到](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)有关如何使用代理的详细信息。

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

此错误表示 CLI 找不到给定注册表的登录服务器, 并且当前云中找不到任何默认后缀。 如果注册表不存在, 或者如果注册表的云和当前 Azure CLI 云不匹配, 或者 Azure CLI 版本已过时, 则会发生此错误, 如果该用户对注册表没有正确的权限, 则会发生此错误。

*可能的解决方案*:验证拼写是否正确以及注册表是否存在;验证用户对注册表具有适当的权限, 并且注册表和 CLI 环境的云是否匹配;将 Azure CLI 更新到最新版本。

## <a name="next-steps"></a>后续步骤

有关检查注册表的运行状况的选项, 请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

请参阅常见问题和 Azure 容器注册表的其他已知问题的[常见问题解答](container-registry-faq.md)。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
