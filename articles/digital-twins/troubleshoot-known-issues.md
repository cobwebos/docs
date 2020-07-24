---
title: 已知问题-Azure 数字孪生
description: 获取帮助识别和缓解 Azure 数字孪生的已知问题。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044142"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 数字孪生中的已知问题

本文介绍与 Azure 数字孪生相关的已知问题。

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