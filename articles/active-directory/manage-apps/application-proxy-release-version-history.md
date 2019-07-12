---
title: Azure AD 应用程序代理：版本发布历史记录 | Microsoft Docs
description: 本文列出 Azure AD 应用程序代理的所有版本，并介绍了新功能和已解决的问题
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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b54d57a13f0f2b47633a25f1a44efdfa17d11a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595059"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 应用程序代理：版本发行历史记录
本文列出了版本和已发布的 Azure Active Directory (Azure AD) 应用程序代理功能。 Azure AD 团队定期更新应用程序代理的新特性和功能。 发布新版本时，应用程序代理连接器会自动更新。

下面是相关资源的列表：

Resource |  详细信息
--------- | --------- |
如何启用应用程序代理 | 启用应用程序代理并安装并注册连接器的必备组件所述[教程](application-proxy-add-on-premises-application.md)。
了解 Azure AD 应用程序代理连接器 | 详细了解如何[连接器管理](application-proxy-connectors.md)以及连接器[自动升级](application-proxy-connectors.md#automatic-updates)。
Azure AD 应用程序代理连接器下载 |  [下载最新的连接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>版本状态

2018 年 9 月 20日日：已发布，供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 添加了对 QlikSense 应用程序的 WebSocket 支持。 若要了解有关如何将 QlikSense 集成使用应用程序代理的详细信息，请参阅此[演练](application-proxy-qlik.md)。 
- 改进了安装向导以使其更轻松地配置出站代理。 
- TLS 1.2 作为默认协议为连接器。 
- 添加新的最终用户许可协议 (EULA)。  

### <a name="fixed-issues"></a>修复的问题

- 修复了导致某些内存泄漏连接器中的 bug。
- 更新 Azure 服务总线版本，其中包括 bug 修复连接器超时问题。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>版本状态

2018 年 1 月 19日日：已发布，供下载

### <a name="fixed-issues"></a>修复的问题

- 需要在 cookie 中的域转换的自定义域添加的支持。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>版本状态 

2017 年 5 月 25日日：已发布，供下载 

### <a name="new-features-and-improvements"></a>新增功能和改进 

改进了的控制连接器的出站连接限制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>版本状态

2017 年 4 月 15日日：已发布，供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 简化的加入和使用较少的所需端口的管理。 现在，应用程序代理要求打开只有两个标准出站端口：443 和 80。 应用程序代理将继续使用仅出站连接，因此您仍不需要外围网络中的任何组件。 有关详细信息，请参阅我们 [配置文档](application-proxy-add-on-premises-application.md)。  
- 如果支持外部代理或防火墙，现在可以打开 dns 网络而不是 IP 范围。 应用程序代理服务需要连接到 *。 msappproxy.net 和 *。 仅 servicebus.windows.net。


## <a name="earlier-versions"></a>早期版本

如果将应用程序代理连接器版本早于 1.5.36.0，更新到最新版本以确保拥有最新版本完全支持的功能。

## <a name="next-steps"></a>后续步骤
- 详细了解如何[远程访问通过 Azure AD 应用程序代理的本地应用程序](application-proxy.md)。
- 若要开始使用应用程序代理，请参阅[教程：添加一个本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)。
