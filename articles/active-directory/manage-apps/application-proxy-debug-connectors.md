---
title: 调试应用程序代理连接器-Azure Active Directory |Microsoft Docs
description: 调试 Azure Active Directory （Azure AD）应用程序代理连接器的问题。
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311851"
---
# <a name="debug-application-proxy-connector-issues"></a>调试应用程序代理连接器问题 

本文将帮助你解决 Azure Active Directory （Azure AD）应用程序代理连接器的问题。 如果正在使用应用程序代理服务远程访问本地 web 应用程序，但连接到应用程序时遇到问题，请使用此流程图调试连接器问题。 

## <a name="before-you-begin"></a>开始之前

本文假设你已安装应用程序代理连接器并且遇到问题。 排查应用程序代理问题时，我们建议你开始处理此故障排除流，以确定是否已正确配置应用程序代理连接器。 如果在连接到应用程序时仍遇到问题，请遵循[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)中的疑难解答流。  


有关应用程序代理和使用其连接器的详细信息，请参阅：

- [通过应用程序代理远程访问本地应用程序](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>连接器问题的流程图

此流程图指导您完成调试某些更常见连接器问题的步骤。 有关每个步骤的详细信息，请参阅 flowchart 后面的表。

![显示连接器调试步骤的流程图](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 操作 | 描述 | 
|---------|---------|---------|
|第 | 查找分配给应用的连接器组 | 您可能在多台服务器上安装了连接器，在这种情况下，应将连接器[分配给连接器组](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 有关连接器组的详细信息，请参阅[使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)。 |
|2 | 安装连接器并分配组 | 如果尚未安装连接器，请参阅[安装和注册连接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br> 如果在安装连接器时遇到问题，请参阅[安装连接器时出现问题](application-proxy-connector-installation-problem.md)。<br></br> 如果未将连接器分配给组，请参阅[将连接器分配给组](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果未将应用程序分配到连接器组，请参阅[将应用程序分配到连接器组](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在连接器服务器上运行端口测试 | 在连接器服务器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他端口测试工具运行端口测试，以检查端口443和80是否已打开。|
|4 | 配置域和端口 | [请确保正确配置了你的域和端口](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)为了使连接器正常工作，某些端口必须是打开的，并且必须是服务器必须能够访问的 Url。 |
|5 | 检查后端代理是否正在使用中 | 检查连接器是否正在使用后端代理服务器，或跳过它们。 有关详细信息，请参阅[排查连接器代理问题和服务连接问题](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新连接器和更新程序以使用后端代理 | 如果正在使用后端代理，则需要确保连接器使用相同的代理。 若要详细了解如何使用代理服务器进行故障排除和配置，请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 在连接器服务器上加载应用程序的内部 URL | 在连接器服务器上，加载应用程序的内部 URL。 |
|8 | 检查内部网络连接 | 内部网络存在连接问题，此调试流无法诊断。 应用程序必须在内部进行访问，连接器才能工作。 可以启用和查看连接器事件日志，如[应用程序代理连接器](application-proxy-connectors.md#under-the-hood)中所述。 |
|9 | 延长后端的超时值 | 在应用程序的**其他设置**中，将 "**后端应用程序超时**" 设置更改为 "**长**"。 请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果问题仍然存在，则针对特定的流问题，查看应用和 SSO 调试流 | 使用[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)诊断流。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何以无提示方式安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)
