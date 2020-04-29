---
title: 排查 Azure 前门配置问题
description: 本教程介绍如何自我排查在使用 Front Door 时可能会遇到的一些常见问题。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471500"
---
# <a name="troubleshooting-common-routing-issues"></a>排查常见的路由问题

本文介绍如何对 Azure 前门配置可能会遇到的一些常见路由问题进行故障排除。

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503几秒钟后，来自前门的响应

### <a name="symptom"></a>症状

- 向后端发送的常规请求无需通过前门就会成功，但通过前门会导致503错误响应。

- 在几秒钟后，前门出现故障（通常在30秒后）

### <a name="cause"></a>原因

当后端超过超时配置（默认为30秒）后，若要接收来自前门的请求或超出此超时值以将响应发送到来自前门的请求，则会出现此症状。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

- 直接将请求发送到后端（无需通过前门），并查看后端响应所需的正常时间。
- 通过前门发送请求，并查看是否看到了503响应。 如果不是，则这可能不是超时问题。 请联系支持人员。
- 如果通过前门导致错误响应代码为503，则配置前门的 sendReceiveTimeout 字段，将默认超时值扩展到4分钟（240秒）。 该设置位于下`backendPoolSettings` ，并且将调用`sendRecvTimeoutSeconds`。 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>发送到自定义域的请求将返回400状态代码

### <a name="symptom"></a>症状

- 您已经创建了一个前门，但向域或前端主机发出的请求返回了 HTTP 400 状态代码。

- 你已创建从自定义域到所配置的前端主机的 DNS 映射。 但是，向自定义域主机名发送请求时返回了 HTTP 400 状态代码，并且该请求似乎未路由到所配置的后端。

### <a name="cause"></a>原因

如果尚未针对添加为前端主机的自定义域配置路由规则，则可能会出现这种症状。 需要为前端主机显式添加路由规则，即使已经在自定义域已 DNS 映射到的 Front Door 子域 (*.azurefd.net) 下面为前端主机配置了一个路由规则，也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

将路由规则从自定义域添加到所需的后端池。

## <a name="front-door-is-not-redirecting-http-to-https"></a>前门未将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

前门有一个路由规则，该规则指示将 HTTP 重定向到 HTTPS，但访问域仍将 HTTP 作为协议进行维护。

### <a name="cause"></a>原因

如果你没有正确配置前门的路由规则，则可能会发生此行为。 基本上，当前配置不特定，并且可能存在冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>对前端主机名发送请求时返回 404 状态代码

### <a name="symptom"></a>症状

- 你已创建了一个 Front Door 并配置了前端主机、至少包含一个后端的后端池，以及用于将前端主机连接到后端池的路由规则。 在向配置的前端主机发送请求时，该配置似乎不可用，因为返回了 HTTP 404 状态代码。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状：

- 后端不是面向公众的后端，并且在前门中不可见。
- 后端配置错误，这会导致前门发送错误的请求（即，后端仅接受 HTTP，但你未被取消选中允许使用 HTTPS，因此前门正在尝试转发 HTTPS 请求）。
- 后端拒绝了连同请求一起转发到后端的主机标头。
- 后端的配置尚未完全部署。

### <a name="troubleshooting-steps"></a>疑难解答步骤

1. 部署时间
   - 请务必等待大约 10 分钟来部署配置。

2. 检查后端设置
    - 导航至请求应路由到的后端池（取决于路由规则的配置方式），并检查后端主机类型和后端主机名是否正确。__ 如果后端是自定义主机，请确保拼写正确。 

    - 检查 HTTP 和 HTTPS 端口。 在大多数情况下，80 和 443（分别为 HTTP 和 HTTPS 端口）是正确的，无需更改。 但是，你的后端可能并未采用这种配置，而是侦听另一个端口。

        - 检查为前端主机应路由到的后端配置的后端主机标头。__ 在大多数情况下，此标头应与后端主机名相同。__ 但是，如果后端需要某种不同的配置，则错误的值可能导致不同的 HTTP 4xx 状态代码。 输入后端的 IP 地址时，可能需要将后端主机标头设置为后端的主机名。__


3. 检查路由规则设置
    - 导航到应该从相关前端主机名路由到后端池的路由规则。 确保接受的协议已正确配置，否则，请确保 Front Door 在转发请求时使用的协议已正确配置。 "_接受的协议_" 字段确定前门应接受的请求，_转发协议_确定应该使用哪种协议前门将请求转发到后端。
         - 例如，如果后端仅接受 HTTP 请求，则以下配置有效：
            - 接受的协议是 HTTP 和 HTTPS。__ 转发协议是 HTTP。__ 匹配请求不起作用，因为 HTTPS 是允许的协议，如果请求作为 HTTPS 传入，则 Front Door 会尝试使用 HTTPS 将其转发。

            - 接受的协议是 HTTP。__ 转发协议是匹配请求或 HTTPS。__

    - “URL 重写”默认已禁用。仅当你要缩小想要提供的后端托管资源的范围时，才使用此字段。__ 禁用时，Front Door 会转发它收到的相同请求路径。 有可能此字段配置不当，而 Front Door 从不可用的后端请求资源，因此返回了 HTTP 404 状态代码。

