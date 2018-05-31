---
title: Azure 应用程序网关的重定向概述
description: 了解 Azure 应用程序网关中的重定向功能
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204371"
---
# <a name="application-gateway-redirect-overview"></a>应用程序网关重定向概述

为确保应用程序及其用户之间的所有通信都通过加密路径进行，适用于许多 Web 应用的常见方案是支持 HTTP 到 HTTPS 自动重定向。 过去用户曾使用创建专用的后端池等技术，其唯一目的在于将通过 HTTP 接收的请求重定向到 HTTPS。

应用程序网关现在可以重定向通过网关的流量。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。 应用程序网关重定向支持并不局限于 HTTP -> HTTPS 重定向。 它是一种泛型重定向机制，使用它可以在应用程序网关上将一个侦听器上接收到的流量重定向到另一个侦听器。 还支持外部站点重定向。

应用程序网关重定向支持具有以下功能：

-  **全局重定向**

   在网关上从一个侦听器重定向到另一个侦听器。 这样可实现站点上的 HTTP 到 HTTPS 重定向。
- **基于路径的重定向**

   这种类型的重定向只能在特定站点区域中进行 HTTP 到 HTTPS 重定向，例如 /cart/* 表示的购物车区域。
- **重定向到外部站点**

![重定向](./media/redirect-overview/redirect.png)

进行此更改后，客户需要创建新的重定向配置对象，以指定重定向需要指向的目标侦听器或外部站点。 配置元素还支持一些选项，通过这些选项可以将 URI 路径和查询字符串追加到重定向的 URL。 你还可以选择重定向是临时重定向（HTTP 状态代码 302）还是永久重定向（HTTP 状态代码 301）。 创建此重定向配置后，会通过新规则将其附加到源侦听器。 使用基本规则时，重定向配置与源侦听器相关联，并且是全局重定向。 使用基于路径的规则时，将在 URL 路径映射中定义重定向配置。 因此，它仅适用于站点的特定路径区域。

### <a name="next-steps"></a>后续步骤

[在应用程序网关上配置 URL 重定向](tutorial-url-redirect-powershell.md)
