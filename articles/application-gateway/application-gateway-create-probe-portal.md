---
title: 使用门户创建自定义探测
titleSuffix: Azure Application Gateway
description: 了解如何使用门户创建应用程序网关的自定义探测
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074604"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用门户创建应用程序网关的自定义探测

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-probe-portal.md)
> * [Azure 资源管理器 PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 经典 PowerShell](application-gateway-create-probe-classic-ps.md)

本文介绍如何通过 Azure 门户将自定义运行状况探测添加到现有应用程序网关。 使用运行状况探测，Azure 应用程序网关监视后端池中资源的运行状况。

## <a name="before-you-begin"></a>开始之前

如果还没有应用程序网关，请访问[创建应用程序网关](application-gateway-create-gateway-portal.md)，创建要使用的应用程序网关。

## <a name="create-probe-for-application-gateway-v2-sku"></a>为应用程序网关 v2 SKU 创建探测

可通过门户分两步配置探测。 第一步是输入探测配置所需的值。 在第二步中，你将使用此探测配置测试后端运行状况，并保存探测。 

### <a name="createprobe"></a>输入探测属性

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)

2. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“partners.contoso.net”， 轻松访问应用程序网关。

3. 选择 "**运行状况探测**"，然后选择 "**添加**" 以添加新的运行状况探测。

   ![添加新探测][4]

4. 在 "**添加运行状况探测**" 页上，填写探测所需的信息，完成后，选择 **"确定"** 。

   |**设置** | **值** | **详细信息**|
   |---|---|---|
   |**名称**|customProbe|此值是可在门户中访问的探测的友好名称。|
   |**协议**|HTTP 或 HTTPS | 运行状况探测使用的协议。 |
   |**主机**|例如 contoso.com|此值是在应用程序服务器上运行的虚拟主机（不同于 VM 主机名）的名称。 探测将发送到（协议）：//（主机名）:(httpsetting 中的端口）/urlPath。  这适用于在应用程序网关上配置了多站点的情况。 如果为单一站点配置了应用程序网关，则输入 "127.0.0.1"。|
   |**从后端 HTTP 设置中选取主机名**|是或否|将探测中的*主机*标头设置为与此探测关联的 HTTP 设置关联的后端池中的后端资源的主机名。 在多租户后端（如 Azure 应用服务）中，特别需要。 [了解详细信息](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Path**|/或另一个路径|自定义探测的完整 URL 的其余部分。 有效路径以“/”开头。 对于默认路径 http:\//contoso.com，只需使用“/” |
   |时间间隔(秒)|30|运行探测来检查运行状况的频率。 不建议将时间间隔设置为不到 30 秒。|
   |超时(秒)|30|探测超时前等待的时间。如果在此超时期间内未收到有效响应，则将探测标记为失败。 超时间隔必须足够长，以便进行 http 调用，确保后端运行状况页可用。 请注意，超时值不应大于此探测设置中使用的 "Interval" 值，也不应与将与此探测关联的 HTTP 设置中的 "请求超时" 值。|
|**不正常阈值**|3|被视为不正常的连续失败尝试次数。 阈值可以设置为 1 或更大值。|
   |**使用探测匹配条件**|是或否|默认情况下，状态代码为 200 到 399 的 HTTP(S) 响应被视为正常。 可以更改后端响应代码或后端响应正文的可接受范围。 [了解详细信息](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP 设置**|从下拉列表中选择|探测将与此处选择的 HTTP 设置关联，因此，将监视与所选 HTTP 设置关联的后端池的运行状况。 它将使用与所选 HTTP 设置中使用的相同的探测请求端口。 您只能选择不与任何其他自定义探测相关联的 HTTP 设置。 <br>请注意，只有这些 HTTP 设置可用于与在此探测配置中选择的协议具有相同协议的关联，并具有与*后端 HTTP 设置开关的选取主机名称*相同的状态。|
   
   > [!IMPORTANT]
   > 仅当该探测器与一个或多个 HTTP 设置关联时，才会监视后端的运行状况。 它将监视与此探测相关联的 HTTP 设置关联的后端池的后端资源。 探测请求将发送到 http：//（主机名）:(httpsetting 中的端口）/urlPath。

### <a name="test-backend-health-with-the-probe"></a>测试后端运行状况和探测

输入探测属性后，可以测试后端资源的运行状况，以验证探测配置是否正确，以及后端资源是否按预期运行。

1. 选择 "**测试**" 并记下探测的结果。 应用程序网关测试后端池中所有后端资源的运行状况，这些资源与用于此探测的 HTTP 设置关联。 

   ![测试后端运行状况][5]

2. 如果存在任何不正常的后端资源，请查看**详细信息**列，了解资源不正常状态的原因。 如果资源由于错误的探测配置被标记为不正常，则选择 "返回**探测**链接"，然后编辑探测配置。 否则，如果该资源由于后端问题被标记为不正常，请解决后端资源的问题，然后通过选择 "**返回探测**链接" 并选择 "**测试**" 再次测试后端。

   > [!NOTE]
   > 您可以选择即使使用不正常的后端资源也保存探测器，但不建议这样做。 这是因为，应用程序网关会从后端池删除这些后端资源，这些后端资源已确定为探测器不正常。 如果后端池中没有正常运行的资源，则将无法访问应用程序，并将收到502错误。

   ![查看探测结果][6]

3. 选择 "**添加**" 以保存探测。 

## <a name="create-probe-for-application-gateway-v1-sku"></a>为应用程序网关 v1 SKU 创建探测

可通过门户分两步配置探测。 第一步是创建探测。 第二步是将探测添加到应用程序网关的后端 http 设置。

### <a name="createprobe"></a>创建探测

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)

2. 在 Azure 门户的 "收藏夹" 窗格中，选择 "**所有资源**"。 在 "**所有资源**" 页中选择应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“partners.contoso.net”， 轻松访问应用程序网关。

3. 选择 "**探测**"，然后选择 "**添加**" 添加探测。

   ![添加填写了信息的“探测”边栏选项卡][1]

4. 在 "**添加运行状况探测**" 边栏选项卡上，填写探测所需的信息，完成后，选择 **"确定"** 。

   |**设置** | **值** | **详细信息**|
   |---|---|---|
   |**名称**|customProbe|此值是可在门户中访问的探测的友好名称。|
   |**协议**|HTTP 或 HTTPS | 运行状况探测使用的协议。 |
   |**主机**|例如 contoso.com|此值是在应用程序服务器上运行的虚拟主机（不同于 VM 主机名）的名称。 探测将发送到（协议）：//（主机名）:(httpsetting 中的端口）/urlPath。  这适用于在应用程序网关上配置了多站点的情况。 如果为单一站点配置了应用程序网关，则输入 "127.0.0.1"。|
   |**从后端 HTTP 设置中选取主机名**|是或否|将探测中的*主机*标头设置为与此探测关联的 HTTP 设置关联的后端池中的后端资源的主机名。 在多租户后端（如 Azure 应用服务）中，特别需要。 [了解详细信息](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Path**|/或另一个路径|自定义探测的完整 URL 的其余部分。 有效路径以“/”开头。 对于默认路径 http:\//contoso.com，只需使用“/” |
   |时间间隔(秒)|30|运行探测来检查运行状况的频率。 不建议将时间间隔设置为不到 30 秒。|
   |超时(秒)|30|探测超时前等待的时间。如果在此超时期间内未收到有效响应，则将探测标记为失败。 超时间隔必须足够长，以便进行 http 调用，确保后端运行状况页可用。 请注意，超时值不应大于此探测设置中使用的 "Interval" 值，也不应与将与此探测关联的 HTTP 设置中的 "请求超时" 值。|
|**不正常阈值**|3|被视为不正常的连续失败尝试次数。 阈值可以设置为 1 或更大值。|
   |**使用探测匹配条件**|是或否|默认情况下，状态代码为 200 到 399 的 HTTP(S) 响应被视为正常。 可以更改后端响应代码或后端响应正文的可接受范围。 [了解详细信息](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > 主机名不同于服务器名。 此值是运行在应用程序服务器上的虚拟主机的名称。 探测发送到 http://(主机名):(httpsetting 中的端口)/urlPath

### <a name="add-probe-to-the-gateway"></a>向网关添加探测

创建探测以后，即可将其添加到网关。 探测设置在应用程序网关的后端 http 设置中设置。

1. 单机应用程序网关的“HTTP 设置”，并单击窗口中列出的当前后端 http 设置，以便显示“配置”边栏选项卡。

   ![https 设置窗口][2]

2. 在 " **appGatewayBackEndHttpSettings**设置" 页上，选中 "**使用自定义探测**" 复选框，并在 "**自定义探测**" 下拉框中选择在 "[创建探测](#createprobe)" 部分创建的探测。
   完成后，单击“保存”即可应用相关设置。

## <a name="next-steps"></a>后续步骤

使用[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)查看探测器确定的后端资源的运行状况。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
