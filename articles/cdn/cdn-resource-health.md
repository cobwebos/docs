---
title: 监视 Azure CDN 资源的运行状况 | Microsoft Docs
description: 了解如何使用 Azure 资源运行状况监视 Azure CDN 资源的运行状况。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "22997922"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>监视 Azure CDN 资源的运行状况
  
Azure CDN 资源运行状况是 [Azure 资源运行状况](../resource-health/resource-health-overview.md)的一部分。  可以使用 Azure 资源运行状况来监视 CDN 资源的运行状况，并获取可操作指南用以解决问题。

>[!IMPORTANT] 
>Azure CDN 资源运行状况目前仅包括全球 CDN 交付和 API 功能的运行状况。  Azure CDN 资源运行状况不会验证各个 CDN 终结点。
>
>反馈 Azure CDN 资源运行状况的信号最多可延迟 15 分钟。

## <a name="how-to-find-azure-cdn-resource-health"></a>如何查找 Azure CDN 资源运行状况

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 CDN 配置文件。

2. 单击“设置”按钮。

    ![“设置”按钮](./media/cdn-resource-health/cdn-profile-settings.png)

3. 在“支持 + 疑难解答”下，单击“资源运行状况”。

    ![CDN 资源运行状况](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>也可以在“帮助 + 支持”边栏选项卡的“资源运行状况”磁贴中找到 CDN 资源。  通过单击门户右上角的圆形 **?**，可 快速转到“帮助 + 支持”。
>
> ![帮助 + 支持](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>特定于 Azure CDN 的消息

可在下面找到与 Azure CDN 资源运行状况相关的状态。

|消息 | 建议的操作 |
|---|---|
|可能已停止、删除或错误配置了一个或多个 CDN 终结点 | 可能已停止、删除或错误配置了一个或多个 CDN 终结点。|
|很抱歉，CDN 管理服务当前不可用 | 请稍后返回此处查看状态更新；如果在预期的解决时间后问题仍然存在，请联系支持人员。|
|很抱歉，CDN 终结点可能因我们的某些 CDN 提供程序当前存在的问题而受到了影响 | 请稍后返回此处查看状态更新；使用故障排除工具了解如何测试源和 CDN 终结点；如果在预期的解决时间后问题仍然存在，请联系支持人员。 |
|很抱歉，CDN 终结点配置更改出现传播延迟 | 请稍后返回此处查看状态更新；如果配置更改未在预期的时间内完全传播，请联系支持人员。|
|很抱歉，加载补充门户时遇到了问题 | 请稍后返回此处查看状态更新；如果在预期的解决时间后问题仍然存在，请联系支持人员。|
很抱歉，我们的某些 CND 提供程序出现了问题 | 请稍后返回此处查看状态更新；如果在预期的解决时间后问题仍然存在，请联系支持人员。 |

## <a name="next-steps"></a>后续步骤

- [阅读 Azure 资源运行状况概述](../resource-health/resource-health-overview.md)
- [排查 CDN 压缩问题](./cdn-troubleshoot-compression.md)
- [排查 404 错误问题](./cdn-troubleshoot-endpoint.md)