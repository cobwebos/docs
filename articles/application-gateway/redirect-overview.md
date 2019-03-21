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
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170048"
---
# <a name="application-gateway-redirect-overview"></a>应用程序网关重定向概述

应用程序网关可用于将流量重定向。  它有一个泛型重定向机制，这允许将重定向到另一个侦听器或外部站点的一个侦听器上接收的通信。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。

许多 web 应用程序的常见重定向方案是支持自动 HTTP 到 HTTPS 重定向，以确保通过加密路径进行应用程序及其用户之间的所有通信。 过去用户曾使用创建专用的后端池等技术，其唯一目的在于将通过 HTTP 接收的请求重定向到 HTTPS。 通过在应用程序网关重定向支持，可以完成此操作只需通过将新的重定向配置添加到路由规则，并使用 HTTPS 协议作为目标侦听器指定另一个侦听器。

支持以下类型的重定向：

- 301 永久重定向
- 302 已找到
- 303 请参阅其他
- 307 临时重定向

应用程序网关重定向支持具有以下功能：

-  **全局重定向**

   在网关上从一个侦听器重定向到另一个侦听器。 这样可实现站点上的 HTTP 到 HTTPS 重定向。
- **基于路径的重定向**

   这种类型的重定向只能在特定站点区域中进行 HTTP 到 HTTPS 重定向，例如 /cart/* 表示的购物车区域。
- **重定向到外部站点**

![重定向](./media/redirect-overview/redirect.png)

进行此更改后，客户需要创建新的重定向配置对象，以指定重定向需要指向的目标侦听器或外部站点。 配置元素还支持一些选项，通过这些选项可以将 URI 路径和查询字符串追加到重定向的 URL。 此外可以选择重定向的类型。 创建此重定向配置后，会通过新规则将其附加到源侦听器。 使用基本规则时，重定向配置与源侦听器相关联，并且是全局重定向。 使用基于路径的规则时，将在 URL 路径映射中定义重定向配置。 因此，它仅适用于站点的特定路径区域。

### <a name="next-steps"></a>后续步骤

[在应用程序网关上配置 URL 重定向](tutorial-url-redirect-powershell.md)
