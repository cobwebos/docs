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
origin.date: 03/19/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715788"
---
# <a name="application-gateway-redirect-overview"></a>应用程序网关重定向概述

可以使用应用程序网关来重定向流量。  它有一种泛型重定向机制，用于将一个侦听器上接收到的流量重定向到另一个侦听器或外部站点。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。

为确保应用程序及其用户之间的所有通信都通过加密路径进行，适用于许多 Web 应用的常见重定向方案是支持 HTTP 到 HTTPS 自动重定向。 过去用户曾使用创建专用的后端池等技术，其唯一目的在于将通过 HTTP 接收的请求重定向到 HTTPS。 由于应用程序网关提供重定向支持，因此你可以很容易地完成此操作，只需向路由规则添加一个新的重定向配置，然后将使用 HTTPS 协议的另一个侦听器指定为目标侦听器即可。

支持以下类型的重定向：

- 301 永久性重定向
- 302 已找到
- 303 参见其他
- 307 临时重定向

应用程序网关重定向支持具有以下功能：

-  **全局重定向**

   在网关上从一个侦听器重定向到另一个侦听器。 这样可实现站点上的 HTTP 到 HTTPS 重定向。
- **基于路径的重定向**

   这种类型的重定向只能在特定站点区域中进行 HTTP 到 HTTPS 重定向，例如 /cart/* 表示的购物车区域。
- **重定向到外部站点**

![重定向](./media/redirect-overview/redirect.png)

进行此更改后，客户需要创建新的重定向配置对象，以指定重定向需要指向的目标侦听器或外部站点。 配置元素还支持一些选项，通过这些选项可以将 URI 路径和查询字符串追加到重定向的 URL。 也可选择重定向的类型。 创建此重定向配置后，会通过新规则将其附加到源侦听器。 使用基本规则时，重定向配置与源侦听器相关联，并且是全局重定向。 使用基于路径的规则时，将在 URL 路径映射中定义重定向配置。 因此，它仅适用于站点的特定路径区域。

### <a name="next-steps"></a>后续步骤

[在应用程序网关上配置 URL 重定向](tutorial-url-redirect-powershell.md)

<!-- Update_Description: wording update -->