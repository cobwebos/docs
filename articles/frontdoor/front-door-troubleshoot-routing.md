---
title: 故障排除 - 使用 Azure Front Door 服务配置排查问题 | Microsoft Docs
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
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736116"
---
# <a name="troubleshooting-common-routing-issues"></a>排查常见的路由问题
本文介绍如何排查在使用 Azure Front Door 服务配置时可能会遇到的一些常见路由问题。 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>主机名不路由到后端并返回 400 状态代码


### <a name="symptom"></a>症状
- 你已创建一个 Front Door，但对前端主机的请求返回了 HTTP 400 状态代码。

  - 你已创建从自定义域到所配置的前端主机的 DNS 映射。 但是，向自定义域主机名发送请求时返回了 HTTP 400 状态代码，并且该请求似乎未路由到所配置的后端。

### <a name="cause"></a>原因
- 如果尚未针对添加为前端主机的自定义域配置路由规则，则可能会出现这种症状。 需要为前端主机显式添加路由规则，即使已经在自定义域已 DNS 映射到的 Front Door 子域 (*.azurefd.net) 下面为前端主机配置了一个路由规则，也是如此。

### <a name="troubleshooting-steps"></a>故障排除步骤
- 将路由规则从自定义域添加到所需的后端池。

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>对前端主机名发送请求时返回 404 状态代码

### <a name="symptom"></a>症状
- 你已创建了一个 Front Door 并配置了前端主机、至少包含一个后端的后端池，以及用于将前端主机连接到后端池的路由规则。 在向配置的前端主机发送请求时，该配置似乎不可用，因为返回了 HTTP 404 状态代码。

### <a name="cause"></a>原因
有多个可能的原因会导致此症状：
 - 后端不是面向公众的后端，对 Front Door 服务不可见。

- 后端配置不当，导致 Front Door 服务发送错误的请求（即，后端仅接受 HTTP，但你未取消选中“允许 HTTPS”，因此，Front Door 会尝试转发 HTTPS 请求）。
- 后端拒绝了连同请求一起转发到后端的主机标头。
- 后端的配置尚未完全部署。

### <a name="troubleshooting-steps"></a>故障排除步骤
1. 部署时间
    - 请务必等待大约 10 分钟来部署配置。

2. 检查后端设置
   - 导航至请求应路由到的后端池（取决于路由规则的配置方式），并检查后端主机类型和后端主机名是否正确。 如果后端是自定义主机，请确保拼写正确。 

   - 检查 HTTP 和 HTTPS 端口。 在大多数情况下，80 和 443（分别为 HTTP 和 HTTPS 端口）是正确的，无需更改。 但是，你的后端可能并未采用这种配置，而是侦听另一个端口。

     - 检查为前端主机应路由到的后端配置的后端主机标头。 在大多数情况下，此标头应与后端主机名相同。 但是，如果后端需要某种不同的配置，则错误的值可能导致不同的 HTTP 4xx 状态代码。 输入后端的 IP 地址时，可能需要将后端主机标头设置为后端的主机名。


3. 检查路由规则设置
     - 导航到应该从相关前端主机名路由到后端池的路由规则。 确保接受的协议已正确配置，否则，请确保 Front Door 在转发请求时使用的协议已正确配置。 接受的协议确定 Front Door 应该接受哪些请求，“高级”选项卡下的“转发协议”确定 Front Door 应该使用哪个协议将请求转发到后端。
          - 例如，如果后端仅接受 HTTP 请求，则以下配置有效：
               - 接受的协议是 HTTP 和 HTTPS。 转发协议是 HTTP。 匹配请求不起作用，因为 HTTPS 是允许的协议，如果请求作为 HTTPS 传入，则 Front Door 会尝试使用 HTTPS 将其转发。

               - 接受的协议是 HTTP。 转发协议是匹配请求或 HTTPS。

   - 单击路由规则配置窗格顶部的“高级”选项卡。 “URL 重写”默认已禁用。仅当你要缩小想要提供的后端托管资源的范围时，才使用此字段。 禁用时，Front Door 会转发它收到的相同请求路径。 有可能此字段配置不当，而 Front Door 从不可用的后端请求资源，因此返回了 HTTP 404 状态代码。

