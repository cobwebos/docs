---
title: "创建自定义探测 - Azure 应用程序网关 - Azure 门户 | Microsoft Docs"
description: "了解如何使用门户创建应用程序网关的自定义探测"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 65e9bba4ce9ac41ae2a9a8c3fa7f661165fc1403
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用门户创建应用程序网关的自定义探测

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 经典 PowerShell](application-gateway-create-probe-classic-ps.md)

在本文中，将通过 Azure 门户向现有应用程序网关添加自定义探测。 如果应用程序包含特定运行状况检查页面。或者未在默认 Web 应用程序上提供成功的响应，那么它们非常适合使用自定义探测。

## <a name="before-you-begin"></a>开始之前

如果还没有应用程序网关，请访问[创建应用程序网关](application-gateway-create-gateway-portal.md)，创建要使用的应用程序网关。

## <a name="createprobe"></a>创建探测

可通过门户分两步配置探测。 第一步是创建探测。 第二步是将探测添加到应用程序网关的后端 http 设置。

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)

1. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“partners.contoso.net”， 轻松访问应用程序网关。

1. 单击“探测”，然后单击“添加”按钮添加探测。

  ![添加填写了信息的“探测”边栏选项卡][1]

1. 在“添加运行状况探测”边栏选项卡上填写探测的必填信息，并在完成时单击“确定”。

  |**设置** | **值** | **详细信息**|
  |---|---|---|
  |**Name**|customProbe|此值是可在门户中访问的探测的友好名称。|
  |**协议**|HTTP 或 HTTPS | 运行状况探测使用的协议。|
  |**主机**|例如  contoso.com|此值是用于探测的主机名。 仅在应用程序网关上配置了多站点的情况下适用，否则使用“127.0.0.1”。 此值与 VM 主机名不同。|
  |**路径**|/或另一个路径|自定义探测的完整 URL 的其余部分。 有效路径以“/”开头。 对于 http://contoso.com 的默认路径，只需使用“/” |
  |时间间隔(秒)|30|运行探测来检查运行状况的频率。 不建议将时间间隔设置为不到 30 秒。|
  |超时(秒)|30|超时之前探测的等待时间。 超时间隔必须足够长，以便进行 http 调用，确保后端运行状况页可用。|
  |**不正常阈值**|3|系统认为不正常的失败尝试次数。 阈值为 0 意味着，如果运行状况检查失败，则会立即将后端确定为不正常。|

  > [!IMPORTANT]
  > 主机名不同于服务器名。 此值是运行在应用程序服务器上的虚拟主机的名称。 探测发送到 http://(主机名):(httpsetting 中的端口)/urlPath

## <a name="add-probe-to-the-gateway"></a>向网关添加探测

创建探测以后，即可将其添加到网关。 探测设置在应用程序网关的后端 http 设置中设置。

1. 单机应用程序网关的“HTTP 设置”，然后单击窗口中列出的当前后端 http 设置，以便显示“配置”边栏选项卡。

  ![https 设置窗口][2]

1. 在“appGatewayBackEndHttpSettings”设置边栏选项卡上，选中“使用自定义探测”复选框，然后在“自定义探测”下拉列表中选择在[创建探测](#createprobe)部分创建的探测。
完成后，单击“保存”即可应用相关设置。

默认探测检查对 Web 应用程序的默认访问权限。 现已创建自定义探测，因此应用程序网关可以使用定义的自定义路径来监视后端服务器的运行状况。 应用程序网关根据所定义的条件检查探测中指定的路径。 如果调用 host:Port/path 时没有返回 HTTP 200-399 状态响应，则在达到不正常阈值后，服务器不再进行轮换。 将继续对不正常的实例进行探测，看其何时恢复正常。 将实例添加回正常的服务器池以后，其流量即可恢复，同时也会继续按用户指定的正常时间间隔对实例进行探测。

## <a name="next-steps"></a>后续步骤

若要了解如何通过 Azure 应用程序网关来配置 SSL 卸载，请参阅[配置 SSL 卸载](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png


