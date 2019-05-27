---
title: 调试应用程序代理应用程序-Azure Active Directory |Microsoft Docs
description: 使用 Azure Active Directory (Azure AD) 应用程序代理应用程序调试问题。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172260"
---
# <a name="debug-application-proxy-application-issues"></a>调试应用程序代理应用程序问题 

本文可帮助你解决使用 Azure Active Directory (Azure AD) 应用程序代理应用程序的问题。 如果使用的远程访问的本地 web 应用程序，应用程序代理服务，但你无法连接到该应用程序，使用此流程图来调试应用程序问题。 

## <a name="before-you-begin"></a>开始之前

排除故障时应用程序代理问题，我们建议你使用连接器启动。 首先，按照中的故障排除流[调试应用程序代理连接器颁发](application-proxy-debug-connectors.md)以确保应用程序代理连接器配置正确。 如果仍遇到问题，请返回到本文，对应用程序进行故障排除。  

有关应用程序代理的详细信息，请参阅：

- [在本地应用程序通过应用程序代理远程访问](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>应用程序问题的流程图

此流程图将引导您完成用于调试的一些与连接到该应用程序更常见的问题的步骤。 有关每个步骤的详细信息，请参阅后流程图的表。

![显示用于调试的应用程序的步骤流程图](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 操作 | 描述 | 
|---------|---------|---------|
|第 | 打开浏览器，访问该应用，并输入你的凭据 | 请尝试使用你的凭据登录到应用，并检查任何与用户相关的错误，如[不能访问此公司应用](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 验证用户分配到应用 | 请确保你的用户帐户有权访问该应用程序从企业网络内的，然后测试中登录到应用中的步骤[测试应用程序](application-proxy-add-on-premises-application.md#test-the-application)。 如果登录问题仍然存在，请参阅[如何解决登录错误](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors)。  |
|3 | 打开浏览器并尝试访问应用 | 如果立即出现错误，检查是否正确配置了应用程序代理。 有关特定错误消息的详细信息，请参阅[应用程序代理故障排除问题和错误消息](application-proxy-troubleshoot.md)。  |
|4 | 检查你的自定义域的设置或对错误进行故障排除 | 如果页面根本不会显示，请确保通过查看正确配置自定义域[使用的自定义域](application-proxy-configure-custom-domain.md)。<br></br>如果页面不会加载并显示错误消息，从而排除故障错误指[应用程序代理故障排除问题和错误消息](application-proxy-troubleshoot.md)。 <br></br>如果需要超过 20 秒才会显示错误消息，可能有连接问题。 转到[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除文章。  |
|5 | 如果问题仍然存在，请转到连接器调试 | 可能有代理与连接器之间或在连接器与后端之间的连接问题。 转到[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除文章。 |
|6 | 发布的所有资源、 检查浏览器开发人员工具，并修复链接 | 请确保发布路径包括所有必要映像、 脚本和应用程序的样式表。 有关详细信息，请参阅[添加到 Azure AD 的本地应用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>发布问题，如中所述使用浏览器的开发人员工具 （在 Internet Explorer 或 Microsoft Edge 的 F12 工具） 并检查[应用程序页无法正确显示](application-proxy-page-appearance-broken-problem.md)。 <br></br>查看用于解决断开的链接中的选项[页面上的链接不起作用](application-proxy-page-links-broken-problem.md)。 |
|7 | 检查网络延迟 | 如果在页面加载缓慢，了解有关以尽量降低网络延迟中的方法[减少延迟的注意事项](application-proxy-network-topology.md#considerations-for-reducing-latency)。 | 
|8 | 请参阅更多故障排除帮助 | 如果问题仍然存在，查找中的其他故障排除文章[应用程序代理故障排除文档](application-proxy-page-appearance-broken-problem.md)。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何以无提示方式安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)
