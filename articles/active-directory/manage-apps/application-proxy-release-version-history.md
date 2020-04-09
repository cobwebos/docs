---
title: Azure AD 应用程序代理：版本发布历史记录 |微软文档
description: 本文列出了 Azure AD 应用程序代理的所有版本，并介绍了新功能和修复问题
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983885"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 应用程序代理：版本发布历史记录
本文列出了已发布的 Azure 活动目录 （Azure AD） 应用程序代理的版本和功能。 Azure AD 团队定期更新具有新功能和功能的应用程序代理。 发布新版本时，应用程序代理连接器将自动更新。 

我们建议您确保为连接器启用自动更新，以确保您具有最新的功能和错误修复。 Microsoft 提供对上次连接器版本和之前一个版本的直接支持。

下面是相关资源的列表：

资源 |  详细信息
--------- | --------- |
如何启用应用程序代理 | [本教程](application-proxy-add-on-premises-application.md)介绍了启用应用程序代理以及安装和注册连接器的先决条件。
了解 Azure AD 应用程序代理连接器 | 详细了解[连接器管理](application-proxy-connectors.md)以及如何[连接器自动升级](application-proxy-connectors.md#automatic-updates)。
Azure AD 应用程序代理连接器下载 |  [下载最新的连接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>版本状态

2020 年 4 月 07 日：发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进
-   连接器仅对所有连接使用 TLS 1.2。 有关详细信息[，请参阅连接器先决条件](application-proxy-add-on-premises-application.md#before-you-begin)。
- 改进了连接器和 Azure 服务之间的信令。 这包括支持连接器和 Azure 服务之间的 WCF 通信的可靠会话，以及 WebSocket 通信的 DNS 缓存改进。
- 支持在连接器和后端应用程序之间配置代理。 有关详细信息，请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="fixed-issues"></a>修复的问题
- 已删除回端口 8080 以进行从连接器到 Azure 服务的通信。
- 为 WebSocket 通信添加了调试跟踪。 
- 在后端应用程序 Cookie 上设置时，已解析保留 SameSite 属性。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>版本状态

2018 年 9 月 20 日：发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 添加了对 QlikSense 应用程序的 WebSocket 支持。 要了解有关如何将 QlikSense 与应用程序代理集成的更多，请参阅本[演练](application-proxy-qlik.md)。 
- 改进了安装向导，以便更轻松地配置出站代理。 
- 将 TLS 1.2 设置为连接器的默认协议。 
- 添加了新的最终用户许可协议 （EULA）。  

### <a name="fixed-issues"></a>修复的问题

- 修复了导致连接器中某些内存泄漏的 Bug。
- 更新了 Azure 服务总线版本，其中包括连接器超时问题的错误修复程序。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>版本状态

2018 年 1 月 19 日：发布供下载

### <a name="fixed-issues"></a>修复的问题

- 添加了对需要 Cookie 中域转换的自定义域的支持。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>版本状态 

2017 年 5 月 25 日：发布供下载 

### <a name="new-features-and-improvements"></a>新增功能和改进 

改进了对连接器出站连接限制的控制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>版本状态

2017 年 4 月 15 日：发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 简化的载入和管理，减少所需的端口。 应用程序代理现在只需要打开两个标准出站端口：443 和 80。 应用程序代理继续仅使用出站连接，因此您仍不需要 DMZ 中的任何组件。 有关详细信息，请参阅我们的 [配置文档](application-proxy-add-on-premises-application.md)。  
- 如果外部代理或防火墙支持，您现在可以通过 DNS 而不是 IP 范围打开网络。 应用程序代理服务仅需要连接到 *.msappproxy.net 和 *.servicebus.windows.net。


## <a name="earlier-versions"></a>早期版本

如果您使用的是应用程序代理连接器版本早于 1.5.36.0，请更新到最新版本以确保您拥有最新的完全支持的功能。

## <a name="next-steps"></a>后续步骤
- 通过[Azure AD 应用程序代理了解有关远程访问本地应用程序](application-proxy.md)的更多信息。
- 要开始使用应用程序代理，请参阅[教程：添加本地应用程序，以便通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)。
