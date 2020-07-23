---
title: 已知问题-Azure 数字孪生
description: 获取帮助识别和缓解 Azure 数字孪生的已知问题。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531100"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 数字孪生中的已知问题

本文介绍与 Azure 数字孪生相关的已知问题。

## <a name="managing-event-routes-in-the-azure-portal"></a>管理 Azure 门户中的事件路由

如果你使用个人[**Microsoft 帐户（MSA）**](https://account.microsoft.com/account/Account)登录到门户，如 *@outlook.com* 帐户，你将看到一个屏幕，提示你在门户中尝试管理事件路由时*需要权限来查看事件路由*，而不考虑你的权限级别。

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="尝试在 Azure 数字孪生实例上创建事件路由时出现权限错误的 Azure 门户屏幕截图":::

### <a name="troubleshooting-steps"></a>疑难解答步骤

当前不能在门户中管理事件路由的用户仍可以使用 Azure 数字孪生 Api 或 CLI 管理事件路由。 若要缓解此问题，建议使用以下工具之一来进行事件路由管理。

有关此操作的说明，请参阅[*操作方法：管理终结点和路由*](how-to-manage-routes.md)。

### <a name="possible-causes"></a>可能的原因

你使用个人[Microsoft 帐户（MSA）](https://account.microsoft.com/account/Account)登录到门户，例如 *@outlook.com* 帐户。 管理 Azure 门户中的事件路由目前仅适用于企业域帐户中的 Azure 用户。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的 "400 客户端错误：错误的请求"

Cloud Shell 中的命令可能会间歇性地失败，并出现错误 "400 客户端错误： url 的错误请求： http://localhost:50342/oauth2/token " 后跟完整堆栈跟踪。

### <a name="troubleshooting-steps"></a>疑难解答步骤

可以通过重新运行该 `az login` 命令并完成后续的登录步骤来解决此情况。

此后，你应该能够重新运行命令。

### <a name="possible-causes"></a>可能的原因

这是 Cloud Shell 中的已知问题的结果：[*从 Cloud Shell 中获取令牌间歇性失败，并出现400客户端错误：错误的请求*](https://github.com/Azure/azure-cli/issues/11749)。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的安全和权限的详细信息：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)