---
title: 调试应用程序代理应用程序 - Azure 活动目录 |微软文档
description: 调试 Azure 活动目录 （Azure AD） 应用程序代理应用程序的调试问题。
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382074"
---
# <a name="debug-application-proxy-application-issues"></a>调试应用程序代理应用程序问题 

本文可帮助您解决 Azure 活动目录 （Azure AD） 应用程序代理应用程序的问题。 如果您使用应用程序代理服务远程访问本地 Web 应用程序，但连接到应用程序时遇到问题，请使用此流程图调试应用程序问题。 

## <a name="before-you-begin"></a>开始之前

在排除应用程序代理问题时，我们建议您从连接器开始。 首先，按照[调试应用程序代理连接器问题的](application-proxy-debug-connectors.md)故障排除流进行操作，以确保正确配置应用程序代理连接器。 如果仍有问题，请返回到本文对应用程序进行故障排除。  

有关应用程序代理的详细信息，请参阅：

- [通过应用程序代理远程访问本地应用程序](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>应用程序问题的流程图

此流程图将引导您完成调试连接到应用程序的一些更常见问题的步骤。 有关每个步骤的详细信息，请参阅流程图下面的表。

![显示调试应用程序步骤的流程图](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 操作 | 描述 | 
|---------|---------|---------|
|1 | 打开浏览器、访问应用并输入凭据 | 尝试使用凭据登录到应用，并检查是否存在任何与用户相关的错误，如[无法访问此公司应用](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 验证用户分配到应用 | 请确保您的用户帐户具有从公司网络内部访问应用的权限，然后按照[测试应用程序](application-proxy-add-on-premises-application.md#test-the-application)中的步骤测试登录到应用。 如果登录问题仍然存在，请参阅[如何排除登录错误](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。  |
|3 | 打开浏览器并尝试访问应用 | 如果立即出现错误，请检查应用程序代理配置是否正确。 有关特定错误消息的详细信息，请参阅[解决应用程序代理问题和错误消息](application-proxy-troubleshoot.md)。  |
|4 | 检查自定义域设置或排除错误 | 如果页面根本不显示，请确保通过查看[使用自定义域正确配置自定义域](application-proxy-configure-custom-domain.md)。<br></br>如果页面未加载并出现错误消息，请参考[疑难解答应用程序代理问题和错误消息](application-proxy-troubleshoot.md)来排除错误。 <br></br>如果出现错误消息的时间超过 20 秒，则可能会出现连接问题。 转到[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除一文。  |
|5 | 如果问题仍然存在，则转到连接器调试 | 代理和连接器之间或连接器和后端之间可能有连接问题。 转到[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除一文。 |
|6 | 发布所有资源、检查浏览器开发人员工具并修复链接 | 确保发布路径包含应用程序所需的所有图像、脚本和样式表。 有关详细信息，请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>使用浏览器的开发人员工具（Internet Explorer 或 Microsoft Edge 中的 F12 工具），并检查应用程序页中描述的发布问题[是否正确](application-proxy-page-appearance-broken-problem.md)显示。 <br></br>查看用于解决[页面上链接](application-proxy-page-links-broken-problem.md)中断开的链接的选项不起作用。 |
|7 | 检查网络延迟 | 如果页面加载缓慢，请了解如何在"注意事项"中最小化网络[延迟，以减少延迟](application-proxy-network-topology.md#considerations-for-reducing-latency)。 | 
|8 | 查看其他故障排除帮助 | 如果问题仍然存在，请在[应用程序代理故障排除文档中](application-proxy-troubleshoot.md)查找其他故障排除文章。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序。](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何静默安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)
