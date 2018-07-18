---
title: 测试 Marketplace 的解决方案模板产品/服务 | Microsoft Docs
description: 了解如何测试 Azure Marketplace 的解决方案模板产品/服务。
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: mbaldwin
ms.openlocfilehash: e789d0996e72c935ed9d5f456f9868b73d5ef4ee
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937829"
---
# <a name="test-your-solution-template-offer-in-staging"></a>在过渡环境中测试解决方案模板产品/服务
过渡表示将产品/服务部署到专用“沙盒”，可先在其中进行测试，再发布到生产。 产品/服务出现在过渡环境中，就像客户已对它进行部署一样。 产品/服务必须经过认证才能推送到过渡环境。

暂存产品/服务后，可以在 [Azure 门户](https://portal.azure.com/)中查看并测试产品/服务。

按照下面的步骤，将产品/服务推送到过渡环境并在 [Azure 门户](https://portal.azure.com/)中进行测试：

1. 转到[发布门户](https://publish.windowsazure.com) > “解决方案模板”选项卡 > 产品/服务 >“发布” > “推送到过渡”。
2. 提供用于预览和测试产品/服务的 Azure 订阅的列表。
3. 使用上一步中所用的订阅 ID，登录到 Azure 预览门户。
4. 在 Azure 预览门户中，针对下面所提及的点至少执行一轮测试：
   * 请确保市场营销内容正确显示在 Azure Marketplace 中。
   * 拓扑的端到端部署。
   * 执行性能测试和压力测试。
   * 确保拓扑符合最佳做法。

## <a name="next-steps"></a>后续步骤
如果对结果感到满意，则可以继续执行最终产品/服务发布阶段的**第 4 步**：[将产品/服务部署到 Marketplace](marketplace-publishing-push-to-production.md)。 否则，对产品/服务进行更改，并重新请求认证。

> [!NOTE]
> 对于市场营销内容更改，认证不是必需的。
> 
> 

有关所有发布者任务的指南，请参阅[入门：如何将产品/服务发布到 Azure Marketplace](marketplace-publishing-getting-started.md)。

