---
title: 调试应用程序代理连接器-Azure Active Directory |Microsoft Docs
description: 使用 Azure Active Directory (Azure AD) 应用程序代理连接器调试问题。
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172230"
---
# <a name="debug-application-proxy-connector-issues"></a>调试应用程序代理连接器问题 

本文可帮助你排查问题 Azure Active Directory (Azure AD) 应用程序代理连接器。 如果使用的远程访问的本地 web 应用程序，应用程序代理服务，但你无法连接到该应用程序，使用此流程图来调试连接器问题。 

## <a name="before-you-begin"></a>开始之前

本文假定你已安装应用程序代理连接器，但出现问题。 排除故障时应用程序代理问题，我们建议使用此故障排除的流，以确定应用程序代理连接器配置是否正确启动。 如果你仍无法连接到该应用程序，请按照中的故障排除流[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)。  


有关应用程序代理和使用其连接器的详细信息，请参阅：

- [在本地应用程序通过应用程序代理远程访问](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>连接器问题的流程图

此流程图将引导您完成用于调试的一些更常见的连接器问题的步骤。 有关每个步骤的详细信息，请参阅后流程图的表。

![显示用于调试的连接器的步骤流程图](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 操作 | 描述 | 
|---------|---------|---------|
|第 | 查找分配给应用的连接器组 | 可能有多个服务器上安装了连接器，连接器应在这种情况下[分配给连接器组](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 有关连接器组的详细信息，请参阅[使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)。 |
|2 | 安装连接器，并将组分配 | 如果您没有安装的连接器，请参阅[安装和注册连接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br>如果连接器未分配到一个组，请参阅[将连接器分配给组](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果应用程序不分配给连接器组，请参阅[分配到连接器组应用程序](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在连接器服务器上运行端口测试 | 在连接器服务器上，通过运行端口测试[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他端口测试工具以检查是否打开端口 443 和 80。|
|4 | 配置域和端口 | [请确保正确配置域和端口](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)连接器才能正常工作，有某些必须在打开的端口和你的服务器必须能够访问的 Url。 |
|5 | 检查后端代理是否正在使用 | 检查连接器是否使用后端代理服务器或绕过它们。 有关详细信息，请参阅[排查连接器代理问题和服务的连接问题](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新的连接器和更新程序若要使用后端代理 | 如果后端代理正在使用中，你将想要确保连接器使用相同的代理服务器。 有关故障排除和将连接器配置为使用代理服务器详细信息，请参阅[使用现有本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 加载应用的连接器服务器上的内部 URL | 在连接器服务器上加载应用的内部 URL。 |
|8 | 检查内部网络连接 | 此调试流不能诊断对内部网络中没有连接问题。 应用程序必须在内部工作的连接器可访问。 您可以启用和查看连接器事件日志中所述[应用程序代理连接器](application-proxy-connectors.md#under-the-hood)。 |
|9 | 延长的后端的超时值 | 在**其他设置**应用程序中，将更改**后端应用程序超时**设置为**长**。 请参阅[添加到 Azure AD 的本地应用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果问题仍然存在，面向特定的流问题、 查看应用程序和调试流的 SSO | 使用[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)故障排除流程。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何以无提示方式安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)
