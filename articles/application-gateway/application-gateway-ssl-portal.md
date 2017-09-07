---
title: "配置 SSL 卸载 - Azure 应用程序网关 - Azure 门户 | Microsoft Docs"
description: "本文说明如何使用 Azure 门户创建支持 SSL 卸载的应用程序网关"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 23b5a529e7ee7db5615340352fb68b2e64e45972
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>使用 Azure 门户配置可以进行 SSL 卸载的应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 经典 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

可将 Azure 应用程序网关配置为在网关上终止安全套接字层 (SSL) 会话，以避免 Web 场中出现开销较高的 SSL 解密任务。 SSL 卸载还简化了 Web 应用程序的前端服务器设置与管理。

## <a name="scenario"></a>方案

以下方案演示了如何在现有应用程序网关上配置 SSL 卸载。 该方案假定已按相关步骤[创建应用程序网关](application-gateway-create-gateway-portal.md)。

## <a name="before-you-begin"></a>开始之前

若要配置应用程序网关的 SSL 卸载，必须提供证书。 此证书在应用程序网关上加载，用于加密和解密通过 SSL 发送的流量。 证书需采用个人信息交换 (.pfx) 格式。 此文件格式适用于导出私钥，后者是应用程序网关对流量进行加解密所必需的。

## <a name="add-an-https-listener"></a>添加 HTTPS 侦听器

HTTPS 侦听器根据配置来查找流量，并可将流量路由到后端池。 若要添加 HTTPS 侦听器，请按照下列步骤操作：

   1. 浏览到 Azure 门户，并选择现有的应用程序网关。

   2. 选择“侦听器”，然后选择“添加”按钮来添加侦听器。

   ![应用程序网关概述窗格][1]


   3. 填写以下侦听器必需的信息并上传 .pfx 证书：
      - 名称：侦听器的友好名称。

      - 前端 IP 配置：用于侦听器的前端 IP 配置。

      - 前端端口（名称/端口）：用在应用程序网关前端的端口的友好名称，以及所使用的实际端口。

      - 协议：一个开关，用于确定前端使用 HTTPS 还是 HTTP。

      - 证书（名称/密码）：如果使用了 SSL 卸载，则需对此设置使用 .pfx 证书， 并需提供友好名称和密码。

   4. 选择“确定”。

![添加侦听器窗格][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>创建规则并将其关联到侦听器

现在已创建侦听器。 接下来，创建一个规则以处理来自侦听器的流量。 规则定义如何将流量路由到基于多个配置设置的后端池。 这些设置包括协议、端口和运行状况探测，以及是否使用基于 cookie 的会话相关性。 若要创建一个规则并将其关联到侦听器，请执行以下步骤：


   1. 选择应用程序网关的“规则”，然后选择“添加”。

   ![应用程序网关规则窗格][3]


   2. 在“添加基本规则”下的“名称”字段中，输入规则的友好名称，然后选择在上一步创建的“侦听器”。 选择适当的“后端池”和“HTTP 设置”，然后选择“确定”。

   ![HTTPS 设置窗口][4]

现在，设置将保存到应用程序网关。 保存这些设置可能需要一段时间，此时间过后才能通过门户或 PowerShell 查看这些设置。 保存完以后，应用程序网关将负责处理流量的加解密。 应用程序网关和后端 Web 服务器之间的所有流量都将通过 HTTP 来处理。 任何通过 HTTPS 启动的需要返回到客户端的通信都会返回到加密的客户端。

## <a name="next-steps"></a>后续步骤

若要了解如何配置 Azure 应用程序网关的自定义运行状况探测，请参阅[创建自定义运行状况探测](application-gateway-create-gateway-portal.md)。

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png


