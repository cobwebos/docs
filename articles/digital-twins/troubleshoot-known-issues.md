---
title: 已知问题-Azure 数字孪生
description: 获取帮助识别和缓解 Azure 数字孪生的已知问题。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 6f57f801f2270819d4a67a49590f5ba61b32afcb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047635"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 数字孪生中的已知问题

本文介绍与 Azure 数字孪生相关的已知问题。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的 "400 客户端错误：错误的请求"

Cloud Shell 中的命令可能会间歇性地失败，并出现错误 "400 客户端错误： url 的错误请求： http://localhost:50342/oauth2/token " 后跟完整堆栈跟踪。

对于 Azure 数字孪生，此操作会影响以下命令组：
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>疑难解答步骤

若要解决此情况，可以 `az login` 在 Cloud Shell 中重新运行该命令并完成后续的登录步骤。 此后，你应该能够重新运行该命令。

另一种解决方案是在您的计算机上 [安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ，以便您可以在本地运行 Azure CLI 命令。 本地 CLI 不会遇到此问题。

### <a name="possible-causes"></a>可能的原因

这是 Cloud Shell 中的已知问题的结果： [*从 Cloud Shell 中获取令牌间歇性失败，并出现400客户端错误：错误的请求*](https://github.com/Azure/azure-cli/issues/11749)。

这会给 Azure 数字孪生实例身份验证令牌和 Cloud Shell 默认的基于 [管理身份](../active-directory/managed-identities-azure-resources/overview.md) 的身份验证提供问题。 正在运行的故障排除步骤 `az login` 会将你从托管标识身份验证中排除，从而逐句通过此问题。

这不会影响或命令组中的 Azure 数字孪生命令 `az dt` `az dt endpoint` ，因为它们使用不同类型的身份验证令牌 (基于 ARM 的) ，这与 Cloud Shell 的托管标识身份验证无关。

## <a name="missing-role-assignment-after-scripted-setup"></a>编写脚本后缺少角色分配

在 [*操作方法：设置实例和身份验证 (脚本) *](how-to-set-up-instance-scripted.md)时，某些用户可能会遇到有关角色分配部分的问题。 此脚本不表示失败，但 *Azure 数字孪生所有者 (预览) * 角色未成功分配给用户，此问题将影响在路上创建其他资源的能力。

若要确定运行脚本后是否成功设置了角色分配，请按照安装程序一文中的 [*验证用户角色分配*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 部分中的说明进行操作。 如果用户未显示此角色，此问题会影响你。

### <a name="troubleshooting-steps"></a>疑难解答步骤

若要解决此问题，可以使用 CLI 或 Azure 门户手动设置角色分配。 

按照以下说明操作：
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>可能的原因

对于使用个人 [Microsoft 帐户 (MSA) ](https://account.microsoft.com/account)登录的用户，用户的主体 ID （用于标识类似于此的命令可能不同于用户的登录电子邮件），这使得脚本难以发现并使用来正确分配角色。

## <a name="issue-with-interactive-browser-authentication"></a>交互式浏览器身份验证问题

使用 1.2.0 ** [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet)库**的版本**1.2.0**在 azure 数字孪生应用程序中编写身份验证代码时，可能会遇到[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)方法问题。

这不是最新版本的库。 最新版本为 **1.2.2**。

受影响的方法在以下文章中使用： 
* [*教程：* 为客户端应用编写代码](tutorial-code.md)
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)
* [*操作说明：使用 Azure 数字孪生 Api 和 Sdk*](how-to-use-apis-sdks.md)

此问题包括当尝试在浏览器窗口中进行身份验证时出现 "AuthenticationFailedException" 错误响应。 浏览器窗口可能无法完全启动，或者似乎已成功对用户进行身份验证，而客户端应用程序仍然失败并出现错误。

### <a name="troubleshooting-steps"></a>疑难解答步骤

若要解决此问题，请更新应用程序以使用 `Azure.Identity` 版本 **1.2.2**。 对于此版本的库，浏览器应按照预期方式进行加载和身份验证。

### <a name="possible-causes"></a>可能的原因

这与库的最新版本 `Azure.Identity` (版本 **1.2.0**) ： [*在使用 InteractiveBrowserCredential 时无法进行身份验证时*](https://github.com/Azure/azure-sdk-for-net/issues/13940)的打开问题有关。

如果在 Azure 数字孪生应用程序中使用版本 **1.2.0** ，或将库添加到项目中，但未指定版本 (，则会看到此问题，这也是默认情况下) 的最新版本。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的安全和权限的详细信息：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)