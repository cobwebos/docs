---
title: Azure 应用程序网关的新增功能
description: 了解 Azure 应用程序网关的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将发生的更改。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863094"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure 应用程序网关有哪些新增功能？

Azure 应用程序网关会不断更新。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能

## <a name="new-features"></a>新增功能

|Feature  |说明  |添加日期  |
|---------|---------|---------|
|标头 CRUD/重写     |现在可以重写 HTTP 标头。 有关分步说明，请参阅[教程：创建应用程序网关和重写 HTTP 标头](tutorial-http-header-rewrite-powershell.md)。|2018 年 12 月|
|WAF 配置和排除列表     |我们添加了更多选项来帮助你配置 WAF 和减少误报。 有关详细信息，请参阅 [Web 应用程序防火墙请求大小限制和排除列表](application-gateway-waf-configuration.md)。|2018 年 12 月|
|自动缩放、区域冗余、静态 VIP 支持预览版     |v2 SKU 有了多方面的改进，例如自动缩放、性能提高，等等。 有关详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md#autoscaling-public-preview)。|2018 年 9 月|
|连接清空     |使用连接清空能够正常删除后端池中的成员。 有关详细信息，请参阅[连接清空](overview.md#connection-draining)。|2018 年 9 月|
|自定义错误页     |使用自定义错误页可在剩余网站的格式中创建错误页。 若要启用此功能，请参阅[创建应用程序网关自定义错误页](custom-error.md)。|2018 年 9 月|
|指标增强     |可以更好地查看应用程序网关的状态和增强的指标。 若要在应用程序网关中启用指标，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。|2018 年 6 月|

## <a name="known-issues"></a>已知问题

- [v2 SKU 中的已知问题](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>后续步骤

有关 Azure 应用程序网关的详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)