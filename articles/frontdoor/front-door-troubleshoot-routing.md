---
title: 排查 Azure 前门配置问题
description: 本教程介绍如何自我排查在使用 Front Door 时可能会遇到的一些常见问题。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630604"
---
# <a name="troubleshooting-common-routing-issues"></a>排查常见的路由问题

本文介绍如何对 Azure 前门配置可能会遇到的一些常见路由问题进行故障排除。

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503几秒钟后，来自前门的响应

### <a name="symptom"></a>症状

* 向后端发送的常规请求无需通过前门就会成功，但通过前门会导致503错误响应。
* 前盖中的故障显示 (几秒钟后，通常在30秒后) 

### <a name="cause"></a>原因

此问题的原因可能是以下两种情况之一：
 
* 后端的时间比配置的超时长 (默认值为30秒) 用于接收来自前门的请求。
* 向前门发送请求所用的时间超过了超时值。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 直接将请求发送到后端 (无需通过前门) ，并查看后端响应所需的正常时间。
* 通过前门发送请求，查看是否收到503响应。 如果不是，则问题可能不是超时问题。 联系支持人员。
* 如果通过前门导致错误响应代码为503，则 `sendReceiveTimeout` 为前门配置字段。  (240 秒) ，你可以将默认超时扩展到4分钟。 该设置位于下 `backendPoolSettings` ，并且将调用 `sendRecvTimeoutSeconds` 。 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>发送到自定义域的请求将返回400状态代码

### <a name="symptom"></a>症状

* 你创建了一个前门，但向域或前端主机发出的请求返回了 HTTP 400 状态代码。
* 为自定义域创建了到你配置的前端主机的 DNS 映射。 但是，向自定义域主机名发送请求将返回 HTTP 400 状态代码。 不会路由到您配置的后端。

### <a name="cause"></a>原因

如果未为作为前端主机添加的自定义域配置路由规则，则会出现此问题。 需要为该前端主机显式添加路由规则。 即使已为前门 ( 子域下的前端主机配置了一个 azurefd.net) 也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

为自定义域添加路由规则以将流量定向到选定的后端池。

## <a name="front-door-doesnt-redirect-http-to-https"></a>前门不会将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

前门有一个路由规则，该规则指示将 HTTP 重定向到 HTTPS，但访问域仍将 HTTP 作为协议进行维护。

### <a name="cause"></a>原因

如果你没有为前门正确配置路由规则，则可能会发生这种情况。 基本上，当前配置不特定，并且可能存在冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>对前端主机名发送请求时返回 404 状态代码

### <a name="symptom"></a>症状

 通过配置前端主机、其中至少包含一个后端的后端池，以及用于将前端主机连接到后端池的路由规则，创建了一个前端。 当你向配置的前端主机发出请求时，你的内容不可用，将返回 HTTP 404 状态代码。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状：

* 后端不是面向公众的后端，并且对前门不可见。
* 后端配置不正确，导致前门发送错误的请求。 换而言之，后端仅接受 HTTP，并且未被取消选中 "允许 HTTPS"。 因此，前门正在尝试转发 HTTPS 请求。
* 后端拒绝了连同请求一起转发到后端的主机标头。
* 尚未完全部署后端的配置。

### <a name="troubleshooting-steps"></a>疑难解答步骤

1. 部署时间
   * 确保已等待大约10分钟，以便部署配置。

2. 检查后端设置
    * 导航到请求应路由到的后端池， (取决于你如何) 配置路由规则。 验证 *后端主机类型* 和后端主机名称是否正确。 如果后端是自定义主机，请确保拼写正确。 

    * 检查 HTTP 和 HTTPS 端口。 大多数情况下，80和 443 (分别) 正确且不需要进行任何更改。 但是，您的后端不是以这种方式配置的，并且正在侦听其他端口。

        * 检查为前端主机应路由到的后端配置的后端主机标头。__ 在大多数情况下，此标头应与后端主机名相同。** 但是，如果后端需要某种不同的配置，则错误的值可能导致不同的 HTTP 4xx 状态代码。 输入后端的 IP 地址时，可能需要将后端主机标头设置为后端的主机名。**

3. 检查路由规则设置：
    * 导航到应该从相关前端主机名路由到后端池的路由规则。 在转发请求时，请确保已正确配置接受的协议。 " *接受的协议* " 字段确定前门应接受的请求。 *转发协议*确定应该使用哪种协议前门将请求转发到后端。
         * 例如，如果后端仅接受 HTTP 请求，则以下配置有效：
            * 接受的协议是 HTTP 和 HTTPS。** 转发协议是 HTTP。** Match 请求不起作用，因为 HTTPS 是允许的协议，如果请求作为 HTTPS 传入，则前门会尝试使用 HTTPS 转发该请求。

            * 接受的协议是 HTTP。** *转发协议* 要么是匹配请求，要么是 HTTP。

    - 默认情况下禁用*Url 重写*。 仅当要缩小要使其可用的后端托管资源范围时，才使用此字段。 禁用时，Front Door 会转发它收到的相同请求路径。 可以 misconfigure 此字段。 因此，当前门请求不可用的后端中的资源时，它将返回 HTTP 404 状态代码。
