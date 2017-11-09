---
title: "为应用程序网关创建基于路径的规则 - Azure 门户 | Microsoft Docs"
description: "了解如何使用 Azure 门户为应用程序网关创建基于路径的规则。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>使用 Azure 门户为应用程序网关创建基于路径的规则

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

借助基于 URL 路径的路由，可根据 HTTP 请求的 URL 路径来关联路由。 它会检查是否有路由连接到为应用程序网关中列出的 URL 配置的后端服务器池，并将网络流量发送到定义的池。 基于 URL 路径的路由的常见用例是，将不同内容类型的请求负载均衡到不同的后端服务器池。

应用程序网关有两种类型的规则：基本规则和基于 URL 路径的规则。 基本规则类型为后端池提供轮循机制服务。 除了提供轮循机制服务之外，基于路径的规则还可以在选择适当后端池时，使用请求 URL 的路径模式。

## <a name="scenario"></a>方案

以下方案在现有应用程序网关中创建基于路径的规则。
实施方案的前提是，已执行[使用门户创建应用程序网关](application-gateway-create-gateway-portal.md)中的步骤。

![URL 路由][scenario]

## <a name="createrule"></a>创建基于路径的规则

基于路径的规则需要有自己的侦听器。 创建此规则之前，请务必先验证是否有可用的侦听器。

### <a name="step-1"></a>步骤 1

转到 [Azure 门户](http://portal.azure.com)，并选择现有应用程序网关。 单击“规则”。

![应用程序网关概述][1]

### <a name="step-2"></a>步骤 2

单击“基于路径”按钮，添加新的基于路径的规则。

### <a name="step-3"></a>步骤 3

“添加基于路径的规则”边栏选项卡分为两个部分。 在第一个部分中，可以定义侦听器、规则名称和默认路径设置。 默认路径设置不适用于基于路径的自定义路由。 “添加基于路径的规则”边栏选项卡的第二个部分可供定义基于路径的规则本身。

**基本设置**

* **名称**：可以在门户中查看的易记规则名称。
* **侦听器**：要用于规则的侦听器。
* **默认后端池**：要用于默认规则的后端。
* **默认 HTTP 设置**：要用于默认规则的 HTTP 设置。

**基于路径的规则设置**

* **名称**：基于路径的规则的易记名称。
* **路径**：规则在转发流量时查找的路径。
* **后端池**：要用于规则的后端。
* **HTTP 设置**：要用于规则的 HTTP 设置。

> [!IMPORTANT]
> “路径”设置列出了要匹配的路径模式。 每个模式都必须以正斜线开头，并且只能以星号结尾。 有效示例为 /xyz、/xyz 和 /xyz/。  

![添加填写了信息的“基于路径的规则”边栏选项卡][2]

通过 Azure 门户，可以非常轻松地将基于路径的规则添加到现有应用程序网关。 创建基于路径的规则后，可以编辑为包含其他规则。 

![添加其他基于路径的规则][3]

此步骤配置基于路径的路由。 大家有必要了解，请求是不会重写的。 当有请求进入时，应用程序网关会检查请求，并根据 URL 模式将请求发送到相应的后端池。

## <a name="next-steps"></a>后续步骤

若要了解如何为 Azure 应用程序网关配置 SSL 卸载，请参阅[使用 Azure 门户配置可以进行 SSL 卸载的应用程序网关](application-gateway-ssl-portal.md)。

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
