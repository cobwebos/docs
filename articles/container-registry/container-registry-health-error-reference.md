---
title: 运行状况检查的 Azure 容器注册表的错误参考
description: 错误代码和通过 Azure 容器注册表中运行 az acr 检查运行状况诊断命令找到问题的可能解决方案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555106"
---
# <a name="health-check-error-reference"></a>运行状况检查错误参考

以下是有关返回的错误代码的详细信息[az acr 检查运行状况][az-acr-check-health]命令。 对于每个错误，列出了可能的解决方案。

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

此错误意味着找不到有关 CLI 的 Docker 客户端。 因此，不运行下列其他检查： 查找 Docker 版本，评估 Docker 守护程序状态和运行 Docker 拉取命令。

*可能的解决方案*:安装 Docker 客户端;将 Docker 路径添加到系统变量。

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

此错误意味着 Docker 守护程序状态为不可用，或者，它无法访问使用 CLI。 因此，Docker 操作 (如`docker login`和`docker pull`) 不可通过 CLI。

*可能的解决方案*:重新启动 Docker 守护程序，或验证已正确安装。

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

此错误表示 CLI 程序无法运行该命令`docker --version`。

*可能的解决方案*:请尝试手动运行该命令，请确保使用最新的 CLI 版本，并调查的错误消息。

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

此错误表示 CLI 不能对您的环境实现的示例图像。

*可能的解决方案*:验证拉取映像所需的所有组件都正常都运行。

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

此错误意味着通过 CLI，它可以阻止其他 Helm 操作找不到该 Helm 客户端。

*可能的解决方案*:验证 Helm 客户端安装了，并且，其路径添加到系统环境变量。

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

此错误意味着 CLI 不能确定安装的 Helm 版本。 如果发生这种情况的 Azure CLI 版本 (或者，如果 Helm 版本) 正在使用已过时。

*可能的解决方案*:更新到最新的 Azure CLI 版本或建议使用 Helm 的版本;手动运行该命令，并调查的错误消息。

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

此错误表示给定的注册表登录服务器的 DNS 其执行 ping 操作，但未响应，这意味着不可用。 这可以表示某些连接问题。 或者，可能不存在注册表，用户可能不具有权限，在注册表中 （若要检索其登录服务器正确），或目标注册表是在不同于在 Azure CLI 中使用云中。

*可能的解决方案*:验证连接;检查注册表中，拼写正确以及存在该注册表;验证用户在其上具有适当的权限和注册表的云是相同的 Azure CLI 中使用。

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

此错误表示给定注册表的质询终结点响应 403 禁止访问 HTTP 状态。 此错误意味着用户不能访问到注册表，最有可能由于虚拟网络配置。

*可能的解决方案*:删除虚拟网络规则，或将当前的客户端 IP 地址添加到允许列表。

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

此错误表示目标注册表的质询终结点没有颁发一个挑战。

*可能的解决方案*:段时间后重试。 如果错误仍然存在，请打开时出现问题 https://aka.ms/acr/issues 。

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此错误表示颁发目标注册表的质询终结点的一种挑战，但注册表不支持 Azure Active Directory 身份验证。

*可能的解决方案*:请尝试以不同的方式进行身份验证，例如，使用管理员凭据。 如果用户需要使用 Azure Active Directory 进行身份验证，打开时出现问题 https://aka.ms/acr/issues 。

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此错误表示，注册表登录服务器未响应使用刷新令牌，以便对目标注册表的访问被拒绝。 如果用户对注册表没有适当的权限或 Azure CLI 的用户凭据已过时，可以发生此错误。

*可能的解决方案*:验证用户是否有注册表; 在适当的权限运行`az login`刷新权限、 标记和凭据。

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此错误表示注册表登录服务器未响应与访问令牌，以便对目标注册表访问被拒绝。 如果用户对注册表没有适当的权限或 Azure CLI 的用户凭据已过时，可以发生此错误。

*可能的解决方案*:验证用户是否有注册表; 在适当的权限运行`az login`刷新权限、 标记和凭据。

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

此错误表示，CLI 找不到给定注册表登录服务器，没有默认后缀未找到当前的云。 如果注册表不存在，如果用户没有适当的权限在注册表中，如果注册表的云和当前的 Azure CLI 云不匹配，或 Azure CLI 版本已过时，可以发生此错误。

*可能的解决方案*:验证拼写正确以及存在注册表;验证该用户对注册表中，具有适当的权限，并且在注册表和 CLI 环境的云与匹配;更新到最新版本的 Azure CLI。

## <a name="next-steps"></a>后续步骤

若要检查是注册表的运行状况的选项，请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

请参阅[常见问题解答](container-registry-faq.md)的一些常见问题和了解 Azure 容器注册表的其他已知的问题。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
