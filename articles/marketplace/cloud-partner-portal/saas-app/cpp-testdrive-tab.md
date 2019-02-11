---
title: Azure SaaS 应用程序产品/服务体验版配置 |Microsoft Docs
description: 在 Azure 市场上为 SaaS 应用程序产品/服务配置体验版。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d6b72a994b034b0bbc735e5cca2ce987a02af61
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195416"
---
# <a name="saas-application-test-drive-tab"></a>SaaS 应用程序“体验版”选项卡

使用“体验版”选项卡为客户提供试用体验。

## <a name="test-drive-benefits"></a>体验版的好处

为客户营造试用版体验是一个最佳的做法，能够促使他们放心地购买。 在提供的试用版选项中，体验版在生成高质量潜在顾客并提高潜在顾客转化率方面，是最有效的一个选项。

体验版为客户提供了与产品主要功能和好处相关的自行试用指南，并在实际的实现方案中进行演示。

## <a name="how-a-test-drive-works"></a>体验版的工作方式

潜在客户在市场中搜索并找到你的应用程序。 客户登录并同意接受使用条款。 此时，客户将收到你的预配置环境来试用固定的小时数，同时你可以开发后续要跟踪的优质潜在顾客。 有关详细信息，请参阅[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/what-is-test-drive)

## <a name="publishing-steps"></a>发布步骤

添加体验版的主要发布步骤如下：

1. 定义你的体验版方案
2. 构建和/或修改资源管理器模板
3. 创建体验版分步手册
4. 发布产品/服务

## <a name="setting-up-a-test-drive"></a>设置体验版

我们根据产品类型、方案和你所在的市场提供了四种不同类型的体验版。
|  **类型**          |  **说明**  |  **设置说明**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure 资源管理器               |    Azure 资源管理器体验版是一个部署模板，其中包含了组成由发布者构建的解决方案的所有 Azure 资源。 适合此类体验版的产品是仅使用 Azure 资源的产品。               |       [Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive)            |
|       已托管             |       托管体验版消除了 Microsoft 托管设置以及用于执行体验版用户预配和取消预配的服务的维护工作的复杂性。             |         [托管的体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/hosted-test-drive)          |
|      逻辑应用              |       逻辑应用体验版是一个部署模板，用来包含所有复杂的解决方案体系结构。 所有 Dynamics 应用程序或自定义产品都应当使用此类型的体验版。            |      [逻辑应用体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/logic-app-test-drive)             |
|       Power BI             |         Power BI 体验版包含一个嵌入式链接，指向某个定制的仪表板。 希望演示交互式 Power BI 视觉对象的任何产品都应使用此类型的体验版。 只需上传你的嵌入式 Power BI URL。          |        [Power BI 体验版](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>Power BI 体验版

使用以下步骤配置体验版。

1. 在“新建产品/服务”下，选择“体验版”。
2. 在“体验版”上，选择“是”。

   ![启用体验版](./media/saas-enable-test-drive.png)

   启用体验版时，你将看到“详细信息”和“技术配置”窗体，下面的屏幕截图中显示了这些窗体。

   ![体验版配置窗体](./media/saas-test-drive-yes.png)

3. 在“详细信息”下，提供以下字段的信息：
  
   - 说明 - 描述你的体验版以及用户可以使用它执行的操作。 可以使用基本 HTML 标记来设置此说明的格式。
   - 用户手册 – 上传客户获得体验版时可以使用的用户手册文档。 此手册必须是 pdf 文件。
   - 体验版演示视频（可选）- 可以为客户提供在获得体验版之前观看的视频（YouTube 或 Vimeo）。 提供视频的 URL。

4. 在“技术配置”下，提供以下字段的信息：

   - 体验版类型 – 从下拉列表中选择“Power BI”。
   - Power BI 仪表板链接 – 提供仪表板的链接。

5. 完成体验版配置后，选择“保存”。


## <a name="next-steps"></a>后续步骤

[“店面详细信息”选项卡](./cpp-storefront-tab.md)